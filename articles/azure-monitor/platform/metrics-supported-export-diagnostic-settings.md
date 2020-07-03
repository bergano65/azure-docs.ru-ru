---
title: Azure Monitor экспортировать метрики платформы через параметры диагностики
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422108"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor экспортировать метрики платформы через параметры диагностики

Azure Monitor предоставляет [метрики платформы](data-platform-metrics.md) по умолчанию без конфигурации. Она предоставляет несколько способов взаимодействия с метриками платформы, включая их диаграмму на портале, доступ к ним через REST API или выполнение запросов с помощью PowerShell или CLI. Полный список метрик платформы, доступных в настоящее время с помощью конвейера консолидированной метрики Azure Monitor, см. в статье [Поддерживаемые метрики](metrics-supported.md) . Чтобы запросить эти метрики и получить к ним доступ, используйте [версию api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий.

Вы можете экспортировать метрики платформы из конвейера Azure Monitor в другие расположения одним из двух способов.
1. Использование [параметров диагностики](diagnostic-settings.md) для отправки в log Analytics, концентраторы событий или службу хранилища Azure.
2. Использование [метрик REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Из-за тонкостей в Azure Monitor серверной части не все метрики могут быть экспортированы с помощью параметров диагностики. В таблице ниже перечислены параметры, которые можно и нельзя экспортировать с помощью параметров диагностики.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Изменение поведения в отношении значений NULL и нулевых значений 
 
Для метрик платформы, которые можно экспортировать с помощью параметров диагностики, существует несколько метрик, для которых Azure Monitor интерпретирует "0" как "NULLS". Это привело к путанице между реальным "0" (порожденным ресурсом) и интерпретированным "0" (NULLS). Вскоре произойдет изменение, и метрики платформы, экспортированные с помощью параметров диагностики, больше не будут экспортировать "0", если только они не были действительно переданы базовым ресурсом. Изменение было запланировано 1 апреля 2020, но было отложено из-за смены приоритета из-за КОВИД-19. 

Обратите внимание на следующее:

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


| Экспортировать с помощью параметров диагностики?  | Уже выдает значения NULL |  ResourceType  |  Метрика  |  метрикдисплайнаме  |  Единицы  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Память: текущая цена очистки  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Память: несжимаемая память очистки  |  Байты  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Память: сжимаемая память очистки  |  Байты  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Потоки: занятые потоки в пуле команд  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Потоки: бездействующие потоки в пуле команд  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Длина очереди заданий пула команд  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Подключение: текущие подключения  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Текущие пользовательские сеансы  |  Счетчик  |  Среднее | 
| Да * * * *  | Нет |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Потоки: занятые потоки продолжительного анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Потоки: бездействующие потоки продолжительного анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Потоки: длина очереди заданий продолжительного анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Память подсистемы M  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Байт исключительного числа модулей M  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU подсистемы M  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Виртуальный байт модуля M  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Пробуксовка памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Память: твердый лимит памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Память: верхний предел памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Память: нижний предел памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Память: ограничение памяти VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Память: использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Потоки: занятые потоки ввода-вывода в пуле обработки заданий  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Потоки: длина очереди заданий ввода-вывода пула обработки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Длина очереди заданий пула обработки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Занятые потоки в пуле запросов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Потоки: бездействующие потоки в пуле запросов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Потоки: длина очереди заданий пула запросов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  Quota  |  Память: квота  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Память: заблокировано квот  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Обработка: преобразовано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Обработка: считано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Обработка: записано строк в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Потоки: занятые потоки быстрого анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Потоки: бездействующие потоки быстрого анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Потоки: длина очереди заданий быстрого анализа  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Число успешных подключений в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Общее число неудачных подключений  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Общее число запросов на подключение  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Память: размер нестраничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Память: размер страничных данных VertiPaq  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Байт виртуальной памяти  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  баккенддуратион  |  Длительность внутренних запросов  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Duration  |  Общая длительность запросов шлюза  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубдроппедевентс  |  Удалены события EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубрежектедевентс  |  Отклоненные события EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубсукцессфулевентс  |  Успешные события EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубсроттледевентс  |  Регулируемые события EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтимедаутевентс  |  Истекло время ожидания событий EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталбитессент  |  Размер событий EventHub  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталевентс  |  Всего событий EventHub  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  евенсубтоталфаиледевентс  |  События EventHub, завершившиеся сбоем  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Неудачные запросы к шлюзу (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Другие запросы к шлюзу (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Успешные запросы к шлюзу (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Всего запросов к шлюзу (устарело)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Неавторизованные запросы к шлюзу (устарели)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  аппкпуусажеперцентаже  |  Процент использования ЦП приложением  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  аппмеморикоммиттед  |  Назначенная память приложения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  аппмеморимакс  |  Максимальная память приложения  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  аппмеморюсед  |  Используемая память приложения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  гкпаусетоталкаунт  |  Счетчик приостановки GC  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  гкпаусетоталтиме  |  Общее время приостановки сборки мусора  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  максолдженмеморипулбитес  |  Максимальный доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  олдженмеморипулбитес  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  олдженпромотедбитес  |  Повысить до старого размера данных поколения  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  системкпуусажеперцентаже  |  Процент использования процессора системой  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатерроркаунт  |  Глобальная ошибка Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатрецеиведбитес  |  Всего получено байт в Tomcat  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуестмакстиме  |  Максимальное время запроса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуесттоталкаунт  |  Общее число запросов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатрекуесттоталтиме  |  Общее время запроса Tomcat  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатреспонсеавгтиме  |  Среднее время запроса Tomcat  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсентбитес  |  Всего отправленных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионактивекурренткаунт  |  Счетчик активности сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионактивемакскаунт  |  Максимальное число активных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионаливемакстиме  |  Максимальное время активности сеанса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионкреатедкаунт  |  Число созданных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионекспиредкаунт  |  Счетчик с истекшим сроком действия сеанса Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  томкатсессионрежектедкаунт  |  Число отклоненных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Аппплатформ/пружина  |  йоунгженпромотедбитес  |  Превращение в размер данных создания Титов.  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Всего заданий  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  тоталупдатедеплойментмачинерунс  |  Total Update Deployment Machine Runs (Общее количество запусков развертывания обновлений для компьютера)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Automation/automationAccounts  |  тоталупдатедеплойментрунс  |  Total Update Deployment Runs (Общее количество запусков развертывания обновлений)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedicated Core Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Creating Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Idle Node Count  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Job Delete Complete Events (События окончания удаления задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Job Delete Start Events (События начала удаления задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Job Disable Complete Events (События окончания отключения задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Job Disable Start Events (События начала отключения задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Job Start Events (События запуска задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Job Terminate Complete Events (События окончания завершения задания)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Job Terminate Start Events (События начала завершения задания)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Leaving Pool Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority Core Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Offline Node Count  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Pool Create Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Pool Delete Complete Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Pool Delete Start Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Pool Resize Complete Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Pool Resize Start Events  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Preempted Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Rebooting Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Reimaging Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Running Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Starting Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Start Task Failed Node Count  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Task Complete Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Task Fail Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Task Start Events  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Low-Priority Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Dedicated Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Unusable Node Count  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Waiting For Start Task Node Count  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Активные ядра  |  Активные ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Активные узлы  |  Активные узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Бездействующие ядра  |  Бездействующие ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Бездействующие узлы  |  Бездействующие узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Задание выполнено  |  Задание выполнено  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Задание Отправлено  |  Задание Отправлено  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Освобождение ядер  |  Освобождение ядер  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Покинуть узлы  |  Покинуть узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Вытесненные ядра  |  Вытесненные ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Прерванные узлы  |  Прерванные узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Процент использования квоты  |  Процент использования квоты  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Всего ядер  |  Всего ядер  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Всего узлов  |  Всего узлов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Непригодные для использования ядра  |  Непригодные для использования ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. BatchAI/рабочие области  |  Неиспользуемые узлы  |  Неиспользуемые узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионакцептед  |  Принятые подключения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионактиве  |  Активные подключения  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  коннектионхандлед  |  Обработанные соединения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  кпуусажеперцентажеиндаубле  |  Процент использования ЦП  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  иореадбитес  |  Считанные байты ввода-вывода  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  иовритебитес  |  Число операций записи ввода-вывода  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  MemoryLimit  |  Ограничение памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  меморюсажеперцентажеиндаубле  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  пендингтрансактионс  |  Ожидающие транзакции  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  процесседблоккс  |  Обработанные блоки  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  процесседтрансактионс  |  Обработанные транзакции  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  куеуедтрансактионс  |  Транзакции в очереди  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  рекуессандлед  |  Обработанные запросы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Блокчейн/Блоккчаинмемберс  |  сторажеусаже  |  Использование хранилища  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits  |  Попаданий в кэш  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cacheLatency  |  Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия))  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses  |  Промахов в кэше  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Shard 9)  |  Счетчик  |  Итог | 
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
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients  |  Подключенные клиенты  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients0  |  Connected Clients (Shard 0)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients1  |  Connected Clients (Shard 1)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients2  |  Connected Clients (Shard 2)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients3  |  Connected Clients (Shard 3)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients4  |  Connected Clients (Shard 4)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients5  |  Connected Clients (Shard 5)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients6  |  Connected Clients (Shard 6)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients7  |  Connected Clients (Shard 7)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients8  |  Connected Clients (Shard 8)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  connectedclients9  |  Connected Clients (Shard 9)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  ошибки  |  ошибки  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys  |  Исключенные ключи  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys0  |  Evicted Keys (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys1  |  Evicted Keys (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys2  |  Evicted Keys (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys3  |  Evicted Keys (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys4  |  Evicted Keys (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys5  |  Evicted Keys (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys6  |  Evicted Keys (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys7  |  Evicted Keys (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys8  |  Evicted Keys (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  evictedkeys9  |  Evicted Keys (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys  |  Ключи с истекшим сроком действия  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys0  |  Expired Keys (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys1  |  Expired Keys (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys2  |  Expired Keys (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys3  |  Expired Keys (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys4  |  Expired Keys (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys5  |  Expired Keys (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys6  |  Expired Keys (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys7  |  Expired Keys (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys8  |  Expired Keys (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  expiredkeys9  |  Expired Keys (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands  |  Операций считывания  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond  |  Количество операций в секунду  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Количество операций в секунду (сегмент 0).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Количество операций в секунду (сегмент 1).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Количество операций в секунду (сегмент 2).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Количество операций в секунду (сегмент 3).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Количество операций в секунду (сегмент 4).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Количество операций в секунду (сегмент 5).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Количество операций в секунду (сегмент 6).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Количество операций в секунду (сегмент 7).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Количество операций в секунду (сегмент 8).  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Количество операций в секунду (сегмент 9).  |  Счетчик  |  Максимальная | 
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
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands  |  Наборы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands1  |  Sets (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands2  |  Sets (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  getcommands5  |  Sets (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Всего операций  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total Operations (Shard 0)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total Operations (Shard 1)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total Operations (Shard 2)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total Operations (Shard 3)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total Operations (Shard 4)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total Operations (Shard 5)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total Operations (Shard 6)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total Operations (Shard 7)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total Operations (Shard 8)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total Operations (Shard 9)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys  |  Всего ключей  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys0  |  Total Keys (Shard 0)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys1  |  Total Keys (Shard 1)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys2  |  Total Keys (Shard 2)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys3  |  Total Keys (Shard 3)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys4  |  Total Keys (Shard 4)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys5  |  Total Keys (Shard 5)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys6  |  Total Keys (Shard 6)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys7  |  Total Keys (Shard 7)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys8  |  Total Keys (Shard 8)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Cache/redis  |  totalkeys9  |  Total Keys (Shard 9)  |  Счетчик  |  Максимальная | 
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
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Bytes/Sec  |  Скорость чтения с диска  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Bytes/Sec  |  Запись на диск  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.classicСompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.ClassicStorage/storageAccounts  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  BlobCount  |  Количество больших двоичных объектов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Блобсервицес  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileCount  |  Количество файлов  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  FileShareCount  |  Количество общих файловых ресурсов  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешарекуота  |  Размер квоты общей папки  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешареснапшоткаунт  |  Число моментальных снимков общей папки  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  филешареснапшотсизе  |  Размер моментального снимка файлового ресурса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Филесервицес  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueCount  |  Количество очередей  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  QueueMessageCount  |  Количество сообщений очереди  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Куеуесервицес  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableCount  |  Количество таблиц  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  TableEntityCount  |  Количество сущностей таблиц  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Классикстораже/storageAccounts/Таблесервицес  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Blocked Calls  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  чарактерстраинед  |  Обученные символы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Преобразованные символы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Client Errors  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  Задержка  |  Задержка  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server Errors  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Длительность речи  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Successful Calls  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Total Calls  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Total Errors  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Всего вызовов токенов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Всего транзакций  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Всего сети  |  Всего сети  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachines  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Входящие потоки  |  Входящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Всего сети  |  Всего сети  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Исходящие потоки  |  Исходящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets;  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Использованные кредиты ЦП  |  Использованные кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Оставшиеся кредиты ЦП  |  Оставшиеся кредиты ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска данных  |  Глубина очереди диска данных (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска данных (байт/с)  |  Скорость чтения с диска данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций чтения с диска данных/с  |  Скорость чтения с диска данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск данных (байт/с)  |  Скорость записи на диск данных (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций записи на диск данных в секунду  |  Скорость записи на диск данных (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Входящие потоки  |  Входящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания входящих потоков  |  Максимальная скорость создания входящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (входящий трафик)  |  Сеть в оплатах (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Всего сети  |  Всего сети  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Сеть (исходящий трафик)  |  Оплата за сеть (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Общее количество сетевых исходящих  |  Общее количество сетевых исходящих  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди диска ОС  |  Глубина очереди диска ОС (Предварительная версия)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций чтения с диска ОС в секунду  |  Скорость чтения с диска ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Операций записи на диск ОС в секунду  |  Скорость записи на диск ОС (операций/с) (предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди на диск ОС  |  Диск ОС длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (байт/с)  |  Скорость чтения с диска ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения с диска ОС (операций/с)  |  Операций чтения с диска ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (байт/с)  |  Скорость записи на диск ОС (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи на диск ОС (операций/с)  |  Операций записи на диск ОС в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Исходящие потоки  |  Исходящие потоки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Максимальная скорость создания исходящих потоков  |  Максимальная скорость создания исходящих потоков (Предварительная версия)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Длина очереди в расчете на диск  |  Диск данных длина очереди (не рекомендуется)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (байт/с)  |  Скорость чтения с диска данных (байт/с) (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость чтения в расчете на диск (операций/с)  |  Операций чтения с диска данных/с (устарело)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (байт/с)  |  Скорость записи на диск данных (байт/с) (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Скорость записи в расчете на диск (операций/с)  |  Операций записи на диск данных в секунду (не рекомендуется)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание в кэш диска данных Premium  |  Попадание на чтение кэша диска данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения кэша на диске данных Premium  |  Промах чтения кэша на диске данных Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Попадание чтения кэша на диске ОС Premium  |  Попадание чтения кэша на диске ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Промах чтения кэша на диске ОС Premium  |  Промах чтения кэша диска ОС Premium (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Загрузка ЦП  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Использование памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Получено байтов по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Передано байт по сети в секунду  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  рундуратион  |  Длительность выполнения  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  сукцессфулпуллкаунт  |  Число успешных операций извлечения  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  сукцессфулпушкаунт  |  Число успешных push-уведомлений  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  тоталпуллкаунт  |  Общее число броских  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.ContainerRegistry/registries  |  тоталпушкаунт  |  Общее число push-уведомлений  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Общее количество доступных ядер ЦП в управляемом кластере  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Общий объем доступной памяти в управляемом кластере  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Состояния для различных условий узла  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Число модулей pod по фазам  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Число модулей pod в состоянии готовности  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  AvailableCapacity  |  Доступная емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  битесуплоадедтоклауд  |  Отправлено облачных байт (устройство)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  битесуплоадедтоклаудпершаре  |  Отправлено облачных байт (общий ресурс)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудреадсраугхпут  |  Пропускная способность скачивания в облаке  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудреадсраугхпутпершаре  |  Пропускная способность скачивания в облаке (общая папка)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудуплоадсраугхпут  |  Пропускная способность передачи в облаке  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  клаудуплоадсраугхпутпершаре  |  Пропускная способность передачи в облаке (общая папка)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  хипервмеморютилизатион  |  Использование памяти при вычислении граничных ресурсов  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  хиперввиртуалпроцессорутилизатион  |  Вычисление ребра — процент ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  никреадсраугхпут  |  Пропускная способность чтения (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  никвритесраугхпут  |  Пропускная способность записи (сеть)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Датабокседже/Датабокседжедевицес  |  Общая емкость составляет  |  Общая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/datafactories  |  FailedRuns и  |  циклы выполнения со сбоем;  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns.  |  успешные циклы выполнения.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  активитиканцелледрунс  |  Отмененные метрики выполнения действий  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ActivityFailedRuns  |  Метрики неудачных выполнений действий.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ActivitySucceededRuns  |  Метрики успешных выполнений действий.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  факторисизеингбунитс  |  Общий размер фабрики (ГБ единиц)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeAvailableMemory  |  Доступная память для среды выполнения интеграции  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  интегратионрунтимеаверажетаскпиккупделай  |  Длительность очереди среды выполнения интеграции  |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  IntegrationRuntimeCpuPercentage  |  Использование ЦП средой выполнения интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  интегратионрунтимекуеуеленгс  |  Длина очереди среды выполнения интеграции  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  максалловедфакторисизеингбунитс  |  Максимально допустимый размер фабрики (единица GB)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  максалловедресаурцекаунт  |  Максимальное число допустимых сущностей  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  пипелинеканцелледрунс  |  Отмененные метрики запуска конвейера  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  PipelineFailedRuns  |  Метрики неудачных выполнений конвейеров.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  PipelineSucceededRuns  |  Метрики успешных выполнений конвейеров.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  ресаурцекаунт  |  Общее число сущностей  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  тригжерканцелледрунс  |  Отмененные метрики запуска триггера  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  TriggerFailedRuns  |  Метрики неудачных запусков триггеров.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataFactory/factories;  |  TriggerSucceededRuns  |  Метрики успешных запусков триггеров.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Cancelled AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Failed AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Successful AU Time  |  Секунды  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Cancelled Jobs  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Failed Jobs  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Successful Jobs  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Данных прочитано  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Записанные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Запросы на чтение  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Общий объем хранилища  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Запросы на запись  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Активные подключения  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Неудачные подключения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Активные подключения  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Неудачные подключения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Процент ввода-вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Задержка репликации в секундах  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Максимальный объем хранилища для журнала сервера  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Процент хранилища для журнала сервера  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Используемый объем хранилища для журнала сервера  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Storage limit  |  Байты  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Активные подключения  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Используемое хранилище резервных копий  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Неудачные подключения  |  Счетчик  |  Итог | 
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
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  active_connections  |  Активные подключения  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  cpu_percent  |  Нагрузка ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  /  |  ОПЕРАЦИЙ ВВОДА-ВЫВОДА  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  memory_percent  |  Процент памяти  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  network_bytes_egress  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  network_bytes_ingress  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  storage_percent  |  Процент хранилища  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Дбфорпостгрескл/serversv2  |  storage_used  |  Storage used  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Devices/Account  |  дигиталтвинс. телеметрии. nodes  |  Заполнитель данных телеметрии для узла Digital двойников  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Отброшенных сообщений C2D  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Доставка сообщений C2D завершена  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Отклоненные сообщения C2D  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Неудачные вызовы прямых методов.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Размер запроса вызовов прямых методов.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Размер ответа вызовов прямых методов.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Успешные вызовы прямых методов.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные из серверной части.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных из серверной части.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Успешные операции чтения с двойников, инициированные из серверной части.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Неудачные обновления двойников, инициированные из серверной части.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Размер обновлений двойников, инициированных из серверной части.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Успешные обновления двойников, инициированные из серверной части.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Срок действия сообщений C2D истек (Предварительная версия)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  конфигурации  |  Configuration Metrics (Метрики конфигурации)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Connected devices (preview) (Подключенных устройств (предварительная версия))  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage  |  Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.egress.Storage.BLOBs  |  Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.latency.Storage  |  Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routing: telemetry messages dropped (Маршрутизация: отброшено сообщений телеметрии)   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routing: telemetry messages orphaned (Маршрутизация: потеряно сообщений телеметрии)   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Число предпринятых попыток отправки сообщений телеметрии.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Количество ошибок регулирования  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Число отправленных сообщений телеметрии.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Неудачные операции чтения с двойников, инициированные устройством.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Размер ответа операций чтения с двойников, инициированных устройством.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Успешные операции чтения с двойников, инициированные устройством.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Неудачные обновления двойников, инициированные устройством.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Размер обновлений двойников, инициированных устройством.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Успешные обновления двойников, инициированные устройством.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Общее количество используемых сообщений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Общее использование данных устройства  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия))  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Connected devices (deprecated) (Подключенные устройства (не рекомендуется))   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Total devices (deprecated) (Всего устройств (не рекомендуется))  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  евентгридделивериес  |  Сетка событий: доставок (Предварительная версия)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  евентгридлатенци  |  Задержка сетки событий (Предварительная версия)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Неудачные отмены заданий.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Успешные отмены заданий.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Завершенные задания  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Неудачные операции создания заданий вызова методов.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Успешные операции создания заданий вызова методов.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Неудачные операции создания заданий обновления двойников.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Успешные операции создания заданий обновления двойников.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Неудачные задания.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Неудачные вызовы получения списка заданий.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Успешные вызовы получения списка заданий.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Неудачные запросы заданий.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Успешные запросы заданий.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total devices (preview) (Всего устройств (предварительная версия))  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Неудачные запросы двойников.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Размер результатов запросов двойников.  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Успешные запросы двойников.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Попытки аттестации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Назначенные устройства  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Попытки регистрации  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Доступная служба хранилища  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Отключение связи Cassandra  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Расходы запросов по Cassandra  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Запросы по Cassandra  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Использование данных  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.DocumentDB/databaseAccounts  |  делетевиртуалнетворк  |  делетевиртуалнетворк  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Число документов  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Квота на документы  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Использование индексов  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Запросы метаданных  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Запросить оплату Mongo  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Запросы Mongo  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестскаунт  |  Частота запросов Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсделете  |  Частота запросов на удаление Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсинсерт  |  Частота запросов на вставку Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестскуери  |  Частота запросов Mongo запросов  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  монгорекуестсупдате  |  Частота запросов на обновление Mongo  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Подготовленная пропускная способность  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Задержка репликации P99  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Доступность службы  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Общее количество запросов  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Общее количество единиц запросов  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  FailureCount  |  Число сбоев  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  SuccessCount  |  Число успехов  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  сукцесслатенци  |  Задержка успешного выполнения  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft. Ентерприсекновледжеграф/Services  |  трансактионкаунт  |  Число транзакций  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft. EventGrid/Domains  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft. EventGrid/Domains  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  DroppedEventCount  |  Удаленные события  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  MatchedEventCount  |  Соответствующие события  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  PublishFailCount  |  Опубликовать события с ошибками  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. EventGrid/Domains  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Dead Lettered Events (Невостребованные события)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Delivery Failed Events (Недоставленные события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Delivered Events (Доставленные события)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Destination Processing Duration (Длительность обработки назначения)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Удаленные события  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Соответствующие события  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Опубликовать события с ошибками  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Опубликовать события с ошибками  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Published Events (Опубликованные события)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  публишсукцесслатенциинмс  |  Задержка успешной публикации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Unmatched Events (Несоответствующие события)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Доступная память  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Невыполненная работа записи.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Записанные сообщения.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Закрытые подключения.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Открытые подключения.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ЦП  |  ЦП  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Входящие сообщения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Входящих запросов  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Исходящие сообщения  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ServerErrors  |  Ошибки сервера.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Выполненные запросы  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Регулируемые запросы.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/clusters  |  UserErrors  |  Ошибки пользователей.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Невыполненная работа записи.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Записанные байты.  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Записанные сообщения.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHABL  |  Архивировать сообщения невыполненной работы (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Пропускная способность сообщений архивации (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Архивирование сообщений (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Входящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Входящие байты (устарели) (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Входящие сообщения (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Исходящие байты (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Исходящие байты (устарели) (не рекомендуется)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Исходящие сообщения (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Неудачные запросы (устарело)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Входящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Входящих запросов  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Входящие сообщения (устаревшие) (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INREQS  |  Входящие запросы (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  INTERR  |  Внутренние ошибки сервера (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Другие ошибки (не рекомендуется)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Исходящие байты.  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Исходящие сообщения (устаревшие) (не рекомендуется)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Ошибки превышения квоты.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  Size  |  Size  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Успешные запросы (устарело)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Ошибки занятости сервера (устарели)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.EventHub/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Запросы к шлюзу по категориям  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Запросы к шлюзу  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.HDInsight/clusters  |  нумактивеворкерс  |  Число активных рабочих ролей  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.HDInsight/clusters  |  скалингрекуестс  |  Запросы на масштабирование  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Пороговое значение метрики  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Наблюдаемая емкость  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Наблюдаемое значение метрики  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Инициированные действия масштабирования  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Insights/Components  |  availabilityResults/Аваилабилитиперцентаже  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  availabilityResults/Count  |  Тесты доступности  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Длительность теста доступности  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Время подключения к сети при загрузке страницы  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Время обработки клиента  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Время получения ответа  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Время отправки запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Время загрузки страницы в браузере  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/count  |  Вызовы зависимостей  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.Insights/Components  |  dependencies/duration  |  Длительность зависимости  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  dependencies/failed  |  Сбои при вызове зависимостей  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/browser  |  Исключения браузера  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  exceptions/count  |  Исключения  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.Insights/Components  |  exceptions/server  |  Исключения сервера  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  pageViews/count  |  Просмотры страниц  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.Insights/Components  |  pageViews/duration  |  Время загрузки страницы для просмотра  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Частота исключений  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Объем доступной памяти  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Обработка ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Скорость ввода-вывода процесса  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Процессорное время  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Количество байтов исключительного использования процессов  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Время выполнения HTTP-запроса  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP requests in application queue (HTTP-запросы в очереди приложений)  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Скорость HTTP-запроса  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/count  |  Запросы сервера  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.Insights/Components  |  requests/duration  |  Время ответа от сервера  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Insights/Components  |  requests/failed  |  Failed requests (Неудачные запросы)  |  Счетчик  |  Счетчик | 
| Нет  | Нет |  Microsoft.Insights/Components  |  запросов и ставок  |  Частота запросов сервера  |  Число/с  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Insights/Components  |  traces/count  |  Трассировки  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Всего попаданий в API службы  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Общая задержка API службы  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Всего результатов для API службы  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  качеутилизатион  |  Использование кэша  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортмакслатенессминутес  |  Максимальное время непрерывного экспорта (мин.)  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортнумофрекордсекспортед  |  Непрерывный экспорт-число экспортированных записей  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортпендингкаунт  |  Число ожидающих непрерывного экспорта  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  континуаусекспортресулт  |  Результат непрерывного экспорта  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  ЦП  |  ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  евентспроцесседфоревенсубс  |  Обработано событий (для концентраторов событий и IoT)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  експортутилизатион  |  Использование экспорта  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионлатенциинсекондс  |  Задержка приема (в секундах)  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Результат приема  |  Счетчик  |  Счетчик | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионутилизатион  |  Использование приема  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  инжестионволумеинмб  |  Объем приема (в МБ)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  Проверки  |  Срок поддержания активности  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Query duration (Длительность запросов)  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стеамингинжестрекуестрате  |  Частота запросов приема потоковой передачи  |  Счетчик  |  ратерекуестсперсеконд | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестдатарате  |  Скорость приема данных потоковой передачи  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестдуратион  |  Длительность приема потоковой передачи  |  Миллисекунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Kusto/Clusters  |  стреамингинжестресултс  |  Поток приема результатов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsCompleted  |  Actions Completed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsFailed  |  Actions Failed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsSkipped  |  Actions Skipped   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsStarted  |  Actions Started   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionsSucceeded  |  Actions Succeeded   |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  ActionThrottledEvents  |  Action Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментконнектормеморюсаже  |  Использование памяти соединителя для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментконнекторпроцессорусаже  |  Использование процессора соединителя для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментворкфловмеморюсаже  |  Использование памяти рабочего процесса для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  интегратионсервицеенвиронментворкфловпроцессорусаже  |  Использование процессора рабочих процессов для среда службы интеграции  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsCancelled  |  Выполнение отменено  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsCompleted  |  Runs Completed  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsFailed  |  Runs Failed  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsStarted  |  Runs Started  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunsSucceeded  |  Runs Succeeded  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  рунстартсроттледевентс  |  Запустить регулируемые события запуска  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  RunThrottledEvents  |  Run Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersCompleted  |  Triggers Completed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersFailed  |  Triggers Failed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersFired  |  Triggers Fired   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersSkipped  |  Triggers Skipped  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersStarted  |  Triggers Started   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggersSucceeded  |  Triggers Succeeded   |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft. Logic/Интегратионсервицеенвиронментс  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  ActionLatency  |  Action Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Actions Completed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Actions Failed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Actions Skipped   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Actions Started   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Actions Succeeded   |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Action Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Action Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Billable Action Executions  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Billable Trigger Executions  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Выставление счетов за внутренние операции  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Выставление счетов за операции стандартного соединителя  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Выставление счетов за операции с использованием хранилища  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Run Failure Percentage  |  Процент  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  RunLatency  |  Run Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Выполнение отменено  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Runs Completed  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsFailed  |  Runs Failed  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsStarted  |  Runs Started  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Runs Succeeded  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  рунстартсроттледевентс  |  Запустить регулируемые события запуска  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Run Success Latency  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Run Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Total Billable Executions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Trigger Fire Latency   |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerLatency  |  Trigger Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Triggers Completed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Triggers Failed   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersFired  |  Triggers Fired   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Triggers Skipped  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Triggers Started   |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Triggers Succeeded   |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Trigger Success Latency   |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Trigger Throttled Events  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Активные ядра  |  Активные ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Активные узлы  |  Активные узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Завершенные запуски  |  Завершенные запуски  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  циклы выполнения со сбоем;  |  циклы выполнения со сбоем;  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие ядра  |  Бездействующие ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Бездействующие узлы  |  Бездействующие узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Освобождение ядер  |  Освобождение ядер  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Покинуть узлы  |  Покинуть узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Сбой развертывание модели  |  Сбой развертывание модели  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  развертывание модели запущено  |  развертывание модели запущено  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  развертывание модели успех  |  развертывание модели успех  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Не удалось зарегистрировать модель  |  Не удалось зарегистрировать модель  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Регистрация модели прошла  |  Регистрация модели прошла  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Вытесненные ядра  |  Вытесненные ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Прерванные узлы  |  Прерванные узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Процент использования квоты  |  Процент использования квоты  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Запущенные запуски  |  Запущенные запуски  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Всего ядер  |  Всего ядер  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Всего узлов  |  Всего узлов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Непригодные для использования ядра  |  Непригодные для использования ядра  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.MachineLearningServices/workspaces  |  Неиспользуемые узлы  |  Неиспользуемые узлы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Maps/accounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Maps/accounts  |  Использование  |  Использование  |  Счетчик  |  Счетчик | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  ассеткаунт  |  Число ресурсов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  ассеткуота  |  Квота ресурса  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  ассеткуотауседперцентаже  |  Процент используемой квоты актива  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  контенткэйполицикаунт  |  Число политик ключей содержимого  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  контенткэйполицикуота  |  Квота политики для ключа содержимого  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  контенткэйполицикуотауседперцентаже  |  Процент использования квоты для ключа содержимого  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  стреамингполицикаунт  |  Число политик потоковой передачи  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  стреамингполицикуота  |  Квота политики потоковой передачи  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices  |  стреамингполицикуотауседперцентаже  |  Процент используемой квоты политики потоковой передачи  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  Requests  |  Requests  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Media/mediaservices/Streamingendpoint  |  SuccessE2ELatency  |  Успешное завершение до окончания задержки  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  гкпаусетоталкаунт  |  Счетчик приостановки GC  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  гкпаусетоталтиме  |  Общее время приостановки сборки мусора  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  максолдженмеморипулбитес  |  Максимальный доступный размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  олдженмеморипулбитес  |  Размер данных старого поколения  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  олдженпромотедбитес  |  Повысить до старого размера данных поколения  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицекпуусажеперцентаже  |  Процент использования ЦП службой  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморикоммиттед  |  Назначенная память службы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморимакс  |  Максимальная память службы  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  сервицемеморюсед  |  Используемая память службы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  системкпуусажеперцентаже  |  Процент использования процессора системой  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатерроркаунт  |  Глобальная ошибка Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрецеиведбитес  |  Всего получено байт в Tomcat  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуестмакстиме  |  Максимальное время запроса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуесттоталкаунт  |  Общее число запросов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатрекуесттоталтиме  |  Общее время запроса Tomcat  |  Миллисекунды  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатреспонсеавгтиме  |  Среднее время запроса Tomcat  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсентбитес  |  Всего отправленных байтов Tomcat  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионактивекурренткаунт  |  Счетчик активности сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионактивемакскаунт  |  Максимальное число активных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионаливемакстиме  |  Максимальное время активности сеанса Tomcat  |  Миллисекунды  |  Максимальная | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионкреатедкаунт  |  Число созданных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионекспиредкаунт  |  Счетчик с истекшим сроком действия сеанса Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  томкатсессионрежектедкаунт  |  Число отклоненных сеансов Tomcat  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Microservices4Spring/Аппклустерс  |  йоунгженпромотедбитес  |  Превращение в размер данных создания Титов.  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Volume pool allocated used (Используемый размер выделенного пула для тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Volume pool total logical size (Общий логический размер пула для тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  AverageReadLatency  |  Средняя задержка чтения  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  AverageWriteLatency  |  Средняя задержка записи  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  ReadIops  |  Read iops (Число операций ввода-вывода в секунду при чтении)  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  VolumeLogicalSize  |  Volume logical size (Логический размер тома)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  VolumeSnapshotSize  |  Volume snapshot size (Размер моментальных снимков в томе)  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes  |  WriteIops  |  Write iops (Количество операций ввода-вывода в секунду при записи)  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  аппликатионгатевайтоталтиме  |  Общее время шлюза приложений  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  авгрекуесткаунтперхеалсихост  |  Количество запросов в минуту на исправный узел  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендконнекттиме  |  Время соединения серверной части  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендфирстбитереспонсетиме  |  Время ответа первого байта внутреннего сервера  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  баккендластбитереспонсетиме  |  Время последнего ответа серверной части  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  баккендреспонсестатус  |  Состояние ответа серверной части  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  блоккедкаунт  |  Распределение правил блокированных запросов брандмауэра веб-приложения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  блоккедреккаунт  |  Число заблокированных запросов брандмауэра веб-приложения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  BytesSent  |  Отправлено байт  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  капаЦитюнитс  |  Текущие единицы мощности  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  клиентртт  |  RTT клиента  |  MilliSeconds  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  компутеунитс  |  Текущие единицы вычислений  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Текущие подключения.  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Невыполненные запросы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Количество работоспособных узлов.  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  матчедкаунт  |  Общее распространение правила брандмауэра веб-приложения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Состояние ответа.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Network/applicationGateways  |  Пропускная способность  |  Пропускная способность  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  тлспротокол  |  Клиентский протокол TLS  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Общее количество запросов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Количество неработоспособных узлов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  аппликатионрулехит  |  Число попаданий правил приложения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  Обработка обработанных  |  Обработанные данные  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  фиреваллхеалс  |  Состояние работоспособности брандмауэра  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  нетворкрулехит  |  Число попаданий сетевых правил  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/azurefirewalls  |  снатпортутилизатион  |  Использование портов SNAT  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/dnszones  |  QueryVolume  |  Объем запросов  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Использование емкости, доступной для наборов записей  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/dnszones  |  RecordSetCount  |  Количество наборов записей  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits  |  арпаваилабилити  |  Доступность ARP  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits  |  бгпаваилабилити  |  Доступность BGP  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  глобалреачбитсинперсеконд  |  глобалреачбитсинперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  глобалреачбитсаутперсеконд  |  глобалреачбитсаутперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  косдропбитсинперсеконд  |  дроппединбитсперсеконд  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft.Network/expressRouteCircuits  |  косдропбитсаутперсеконд  |  дроппедаутбитсперсеконд  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft. Network/Експрессраутегатевайс  |  ергатевайконнектионбитсинперсеконд  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| Нет  | Нет |  Microsoft. Network/Експрессраутегатевайс  |  ергатевайконнектионбитсаутперсеконд  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  админстате  |  админстате  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  линепротокол  |  линепротокол  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  портбитсинперсеконд  |  BitsInPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  портбитсаутперсеконд  |  BitsOutPerSecond  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  ркслигхтлевел  |  ркслигхтлевел  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Network/Експрессраутепортс  |  ткслигхтлевел  |  ткслигхтлевел  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Работоспособность серверного компонента (в процентах)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Число запросов к серверному компоненту  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Backend Request Latency (Задержка запросов к серверным частям)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  биллаблереспонсесизе  |  Оплачиваемый размер ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestCount  |  Число запросов  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  RequestSize  |  Размер запроса  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Размер ответа  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Total Latency (Общая задержка)  |  MilliSeconds  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения)  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия))  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  ByteCount  |  Количество байтов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Health Probe Status (Состояние пробы работоспособности)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  PacketCount  |  Количество пакетов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Количество подключений SNAT  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  SYNCount  |  Количество пакетов SYN  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия))  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Получено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Отправлено байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Получено пакетов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Отправлено пакетов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Среднее время приема-передачи (МС)  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  чекксфаиледперцент  |  Процент неудачных проверок (Предварительная версия)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Доля проб с ошибками, в процентах  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/networkWatchers/connectionMonitors  |  раундтриптимемс  |  Время приема-передачи (МС) (Предварительная версия)  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Количество байтов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Удаленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Перенаправленные входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Входящие байты (атака DDoS)  |  Байт/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Входящие пакеты SYN для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Входящие пакеты TCP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Входящие пакеты UDP для активации защиты от атаки DDoS  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Выполняется ли атака DDoS  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Количество пакетов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Удаленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Перенаправленные входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Входящие пакеты (атака DDoS)  |  Число/с  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Количество пакетов SYN  |  Счетчик  |  Итог | 
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
| **Да**  | нет |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Data Path Availability (Доступность пути к данным)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Проверка состояния конечной точки с помощью конечной точки  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Запросы, выполняемые возвращаемой конечной точкой  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway S2S Bandwidth (Пропускная способность шлюза S2S)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S Bandwidth (Пропускная способность шлюза P2S)  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S Connection Count (Количество подключений P2S)  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Пропускная способность туннеля  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Исходящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Удаленные входящие пакеты туннеля (несоответствие селектора трафика)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Исходящие пакеты туннеля  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Входящие байты туннеля  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Входящие пакеты туннеля  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Network/virtualNetworks  |  пингмешаверажераундтрипмс  |  Время кругового пути для проверки связи с виртуальной машиной  |  MilliSeconds  |  Среднее | 
| **Да**  | нет |  Microsoft.Network/virtualNetworks  |  пингмешпробесфаиледперцент  |  Неудачные проверки связи с виртуальной машиной  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  Входящие сообщения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Incoming.all.failedrequests  |  Все неудачные входящие запросы  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Все входящие запросы  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Отправлено запланированных push-уведомлений  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Отменено запланированных push-уведомлений  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Операции управления установкой  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Операции удаления установки  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Операции получения установки  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Операции исправления установки  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Операции создания или обновления установки  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Все исходящие уведомления  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Ошибки из-за поврежденного или просроченного канала  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Ошибки канала  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Ошибки полезных данных  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Ошибки внешней системы уведомлений  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Успешные уведомления  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Ошибка из-за поврежденного канала APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Ошибка из-за просроченного канала APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Ошибки авторизации APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Ошибки APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Успешные уведомления APNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Ошибки проверки подлинности GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Ошибка из-за поврежденного канала GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Ошибка из-за просроченного канала GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Ошибки авторизации GCM (недопустимые учетные данные)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Недопустимый формат уведомления GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Ошибки GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Успешные уведомления GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Регулируемые уведомления GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Ошибка из-за неправильного канала GCM  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Ошибки проверки подлинности MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Ошибка из-за поврежденного канала MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Канал MPNS отсоединен  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  Пропущенные уведомления MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Недопустимые учетные данные MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Недопустимый формат уведомления MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Ошибки MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Успешные уведомления MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Регулируемые уведомления MPNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Ошибки проверки подлинности WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Ошибка из-за поврежденного канала WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Канал WNS отсоединен  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Канал WNS регулируется  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  Пропущенные уведомления WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Ошибка из-за просроченного канала WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Ошибки авторизации WNS (недопустимые учетные данные)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Недопустимый формат уведомления WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Ошибка из-за недопустимого размера уведомления WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Ошибки авторизации WNS (недопустимый маркер)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Ошибки WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Успешные уведомления WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Регулируемые уведомления WNS  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Ошибки авторизации WNS (нет доступа)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Ошибки авторизации WNS (неправильный маркер)  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  Операции регистрации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Операции создания регистрации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Операции удаления регистрации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Операции чтения регистрации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Операции обновления регистрации  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Запланированных уведомлений в состоянии ожидания  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  Процент доступной памяти  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  Процент доступной области подкачки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  Использование выделенной памяти (в байтах), %  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  Процент времени DPC  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  Процент свободных индексных дескрипторов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  Процент свободного места  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  Процент времени простоя  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  Процент времени прерывания  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  Процент времени ожидания ввода-вывода  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  Процент времени работы с низким приоритетом  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  Процент времени работы в привилегированном режиме  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % загруженности процессора  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  Процент используемых индексных дескрипторов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  Процент используемой памяти  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  Процент используемого места  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  Процент используемой области подкачки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  Процент времени пользователя  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Доступный объем в МБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Доступный объем памяти в МБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Доступный объем подкачки в МБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Среднее время чтения с диска (с)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время чтения с диска (с)  |  Среднее время чтения с диска (с)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время обращения к диску (с)  |  Среднее время обращения к диску (сек.)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Среднее время записи на диск (сек.)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. время записи на диск (с)  |  Среднее время записи на диск (сек.)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Полученных байтов/с  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Отправленных байтов/с  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Всего байтов/с  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Текущая длина очереди диска  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Скорость чтения с диска (байт/с)   |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду   |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Операций чтения с диска в секунду   |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Обращений к диску в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |   Скорость записи на диск (байт/с)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Операций записи на диск в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Операций записи на диск в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Свободно мегабайт  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Объем свободной физической памяти  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Объем свободного места в файлах подкачки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Объем свободной виртуальной памяти  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Скорость обмена с логическим диском (байт/с)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Операций чтения со страницы в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Операций записи на страницу в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Страниц в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Процент времени работы в привилегированном режиме  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Процент времени пользователя  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Скорость обмена с физическим диском (байт/с)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Процессы  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  Длина очереди процессора  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Объем, сохраненный в файлах подкачки  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Всего байт  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Всего получено байт  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Всего передано байт  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Всего конфликтов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Всего получено пакетов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Всего передано пакетов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Всего ошибок Rx  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Всего ошибок Tx  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Время доступности  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Используемая области подкачки в МБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Используемая память в КБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Используемый объем памяти в МБ  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Пользователи  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Виртуальная общая память  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  событие  |  событие  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.OperationalInsights/workspaces  |  Пульс  |  Пульс  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.OperationalInsights/workspaces  |  Обновление:  |  Обновление:  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Пробуксовка памяти (наборы данных)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Высокая загрузка QPU  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Длительность запроса (наборы данных)  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Длина очереди заданий пула запросов (наборы данных)  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  SearchLatency  |  Search Latency  |  Секунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Search queries per second  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Throttled search queries percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Число активных сообщений в очереди или разделе.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Закрытые подключения.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Открытые подключения.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  ЦП (не рекомендуется)  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  деадлеттередмессажес  |  Количество недоставленных сообщений в очереди или разделе.  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Входящие сообщения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Входящих запросов  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Сообщения  |  Число сообщений в очереди или разделе.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  намеспацекпуусаже  |  ЦП  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  намеспацемеморюсаже  |  Использование памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Исходящие сообщения  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  счедуледмессажес  |  Число запланированных сообщений в очереди или разделе.  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Ошибки сервера.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  Size  |  Size  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Выполненные запросы  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Регулируемые запросы.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Ошибки пользователей.  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Использование памяти (не рекомендуется)  |  Процент  |  Максимальная | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  актуалкпу  |  актуалкпу  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  актуалмемори  |  актуалмемори  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аллокатедкпу  |  аллокатедкпу  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аллокатедмемори  |  аллокатедмемори  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  аппликатионстатус  |  аппликатионстатус  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  контаинерстатус  |  контаинерстатус  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  График  |  График  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  меморютилизатион  |  меморютилизатион  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  рестарткаунт  |  рестарткаунт  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  сервицерепликастатус  |  сервицерепликастатус  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft. Сервицефабрикмеш/приложения  |  сервицестатус  |  сервицестатус  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Число подключений  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Inbound Traffic (Входящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Количество сообщений  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Outbound Traffic (Исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Системные ошибки  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Ошибки пользователей  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Average CPU percentage (Средний процент использования ЦП)  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Количество считанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Количество записанных байт ввода-вывода  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  io_requests  |  IO requests count (Количество запросов ввода-вывода)  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Зарезервированное дисковое пространство  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Использованное дисковое пространство  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Virtual core count (Число виртуальных ядер)  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers  |  database_storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers  |  dtu_used  |  DTU used  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers  |  storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Выплата за ЦП приложения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Процент загрузки ЦП приложения  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Процент памяти приложения  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Заблокировано брандмауэром  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Cache hit percentage (Процент попаданий в кэш)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Cache used percentage (Процент использования кэша)  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Неудачные подключения  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Успешные подключения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Ограничение ЦП  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Используемый ЦП  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  взаимоблокировка  |  Взаимоблокировки  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Лимит DTU  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU used  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU percentage  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Лимит DWU  |  Счетчик  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU used  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Local tempdb percentage (Процент использования локальной базы данных tempdb)  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Процент памяти  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  носителей.  |  Место, занятое данными  |  Байты  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Размер файла данных tempdb, КБ  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Размер файла журнала tempdb, КБ  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Используемый журнал в процентах tempdb  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Процент выделенного пространства данных  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Ограничение ЦП  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Используемый ЦП  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Выделенное пространство данных  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Ограничение ЦП  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Процент использования ЦП  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Используемый ЦП  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU used  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Процент использования DTU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU limit  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU used  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log IO percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Процент операций ввода/вывода данных  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Процент использования сеансов  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Максимальный размер данных  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Процент использования пространства данных  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Место, занятое данными  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Размер файла данных tempdb, КБ  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Размер файла журнала tempdb, КБ  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Используемый журнал в процентах tempdb  |  Процент  |  Максимальная | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Workers percentage  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Процент хранилища выполняющейся в памяти OLTP  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Используемая емкость  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Емкость больших двоичных объектов  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Количество больших двоичных объектов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Количество контейнеров больших двоичных объектов  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Емкость индекса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Емкость файла  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Количество файлов  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Количество общих файловых ресурсов  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешарекуота  |  Размер квоты общей папки  |  Байты  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешареснапшоткаунт  |  Число моментальных снимков общей папки  |  Счетчик  |  Среднее | 
| Нет  | Нет |  Microsoft.Storage/storageAccounts/fileServices  |  филешареснапшотсизе  |  Размер моментального снимка файлового ресурса  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Емкость очереди  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Количество очередей  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Количество сообщений очереди  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Доступность  |  Доступность  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Исходящие  |  Исходящие  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Входящий трафик  |  Входящий трафик  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Успешно выполненная задержка E2E  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Успешно выполненная задержка сервера  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Емкость таблицы  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Количество таблиц  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Количество сущностей таблиц  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft.Storage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентиопс  |  Всего операций ввода-вывода клиента  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентлатенци  |  Средняя задержка клиента  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентлоккиопс  |  Операции ввода-вывода блокировки клиента  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентметадатареадиопс  |  ОПЕРАЦИЙ чтения метаданных клиента  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентметадатавритеиопс  |  Запись метаданных клиента в операции ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентреадиопс  |  Операции чтения клиента в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентреадсраугхпут  |  Средняя пропускная способность чтения кэша  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентвритеиопс  |  Число операций записи клиента в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  клиентвритесраугхпут  |  Средняя пропускная способность записи в кэш  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетасинквритесраугхпут  |  Пропускная способность асинхронной записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетфиллсраугхпут  |  Пропускная способность заполнения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржесеалс  |  Работоспособность целевого объекта хранилища  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетиопс  |  Всего Сторажетаржет операций ввода-вывода  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетлатенци  |  Задержка Сторажетаржет  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетметадатареадиопс  |  Сторажетаржет чтение метаданных операций ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетметадатавритеиопс  |  Сторажетаржет запись метаданных в секунду  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетреадахеадсраугхпут  |  Пропускная способность чтения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетреадиопс  |  Сторажетаржет чтение операций ввода-вывода  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетсинквритесраугхпут  |  Пропускная способность синхронной записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржеттоталреадсраугхпут  |  Общая пропускная способность чтения Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржеттоталвритесраугхпут  |  Общая пропускная способность записи Сторажетаржет  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  сторажетаржетвритеиопс  |  Сторажетаржет запись в секунду  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. Сторажекаче/кэши  |  Время доступности  |  Время доступности  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  серверсинксессионресулт  |  Результат сеанса синхронизации  |  Счетчик  |  Среднее | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекалледнетворкбитесбяппликатион  |  Размер отзыва по уровням облака по приложениям  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекалледтоталнетворкбитес  |  Размер отзыва при уровне облака  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекаллиототалсизебитес  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинкрекаллсраугхпутбитесперсеконд  |  Пропускная способность отзыва облачного уровня  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксерверхеартбеат  |  Состояние сервера в сети  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес  |  сторажесинксинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс  |  серверхеартбеат  |  Состояние сервера в сети  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс  |  серверрекаллиототалсизебитес  |  Отзыв уровней в облаке  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупсинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс  |  синкграупсинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтбатчтрансферредфилебитес  |  Синхронизировано байт  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтсинксессионапплиедфилескаунт  |  Синхронизировано файлов  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс  |  серверендпоинтсинксессионперитемеррорскаунт  |  Несинхронизирующиеся файлы  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Неудачные запросы функций  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  События функций  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Запросы функций  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Ошибки преобразования данных  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Ошибки десериализации входа  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Неупорядоченные события  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Ранние входные события  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ошибки  |  Ошибки среды выполнения  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Байты входного события  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Входные события  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  инпутевентссаурцесбакклогжед  |  Необработанные входные события  |  Счетчик  |  Максимальная | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  инпутевентссаурцесперсеконд  |  Полученные входные источники  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  События позднего ввода  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Выходные события  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Предельная задержка  |  Секунды  |  Максимальная | 
| **Да**  | нет |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Использование единиц потоковой передачи (%)  |  Процент  |  Максимальная | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Скорость чтения с диска  |  Скорость чтения с диска  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  Число/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадбитесперсеконд  |  Disk Read Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадлатенци  |  Задержка чтения с диска  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискреадоператионс  |  Операции чтения с диска  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дисквритебитесперсеконд  |  Disk Write Bytes/Sec  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дискврителатенци  |  Задержка записи на диск  |  Миллисекунды  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  дисквритеоператионс  |  Операции записи на диск  |  Счетчик  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  меморяктиве  |  Память активна  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  меморигрантед  |  Предоставленная память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  MemoryUsed  |  Используемая память  |  Байты  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Сеть (входящий трафик)  |  Сеть (входящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Сеть (исходящий трафик)  |  Сеть (исходящий трафик)  |  Байты  |  Итог | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  нетворкинбитесперсеконд  |  Сеть в байтах/с  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  нетворкаутбитесперсеконд  |  Сетевых исходящих байт/с  |  Байт/с  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Процент  |  Среднее | 
| **Да**  | нет |  Microsoft. Вмвареклаудсимпле/virtualMachines  |  перцентажекпуреади  |  Процент готовности ЦП  |  Миллисекунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Активные запросы  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Длина дисковой очереди  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  HTTP 2xx  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP 3xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  HTTP 406:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  HTTP 4xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Ошибки HTTP-сервера:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Длина очереди HTTP:  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Крупный"  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Рабочие процессы плана службы приложений уровня "Средний"  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Requests  |  Requests  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Рабочие роли плана службы приложений уровня "Малый"  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Общее число внешних интерфейсов  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Доступные рабочие процессы  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Общее количество рабочих процессов  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Использованные рабочие процессы  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Процент использования ЦП  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Длина дисковой очереди  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Длина очереди HTTP:  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Процент использования памяти  |  Процент  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпклосеваит  |  Ожидание TCP-закрытия  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпклосинг  |  TCP-закрытие  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпестаблишед  |  Установлен TCP  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Ожидание TCP FIN 1  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Ожидание TCP FIN 2  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпластакк  |  Последнее подтверждение TCP  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпсинрецеивед  |  Получен TCP SYN  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткпсинсент  |  TCP SYN отправлен  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/serverfarms  |  ткптимеваит  |  Время ожидания TCP  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AppConnections  |  Соединения  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CpuTime  |  Время ЦП:  |  Секунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Текущее число сборок  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Function Execution Count  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Function Execution Units  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen0Collections  |  Сборок мусора поколения 0  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen1Collections  |  Сборок мусора поколения 1  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Gen2Collections  |  Сборок мусора поколения 2  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Маркеры  |  Счетчик дескрипторов  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  хеалсчеккстатус  |  Состояние проверки работоспособности  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http2xx  |  HTTP 2xx  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http3xx  |  HTTP 3xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http401  |  HTTP 401:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http403  |  HTTP 403:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http404  |  HTTP 404:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http406  |  HTTP 406:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http4xx  |  HTTP 4xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Http5xx  |  Ошибки HTTP-сервера:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  хттпреспонсетиме  |  Время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  PrivateBytes  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Requests  |  Requests  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  Потоки  |  Счетчик потоков  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomains  |  Всего доменов приложений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AppConnections  |  Соединения  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  средний размер рабочего набора памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Среднее время ответа:  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Входящие данные:  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  BytesSent  |  Выходные данные  |  Байты  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CpuTime  |  Время ЦП:  |  Секунды  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Текущее число сборок  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Function Execution Count  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Function Execution Units  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Сборок мусора поколения 0  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Сборок мусора поколения 1  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Сборок мусора поколения 2  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Маркеры  |  Счетчик дескрипторов  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  хеалсчеккстатус  |  Состояние проверки работоспособности  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http2xx  |  HTTP 2xx  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http3xx  |  HTTP 3xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http404  |  HTTP 404:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http406  |  HTTP 406:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http4xx  |  HTTP 4xx:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Http5xx  |  Ошибки HTTP-сервера:  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  хттпреспонсетиме  |  Время ответа  |  Секунды  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Операции ввода-вывода: прочие, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Операции ввода-вывода: прочих операций в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  Операции ввода-вывода: чтение, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Операции ввода-вывода: операций чтения в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Операции ввода-вывода: запись, байт в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Операции ввода-вывода: операций записи в секунду  |  Байт/с  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  рабочий набор памяти;  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Байты исключительного пользования  |  Байты  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Requests  |  Requests  |  Счетчик  |  Итог | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Запросов в очереди приложений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  Потоки  |  Счетчик потоков  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Всего доменов приложений  |  Счетчик  |  Среднее | 
| **Да**  | **Да** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Всего выгруженных доменов приложений  |  Счетчик  |  Среднее | 
