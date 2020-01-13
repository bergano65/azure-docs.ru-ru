---
title: Azure Monitor экспортировать метрики платформы через параметры диагностики
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 1bab6a990ba38bc32c2b7350bb1d4eac15a7b95e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479037"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor экспортировать метрики платформы через параметры диагностики

Azure Monitor предоставляет [метрики платформы](data-platform-metrics.md) по умолчанию без конфигурации. Она предоставляет несколько способов взаимодействия с метриками платформы, включая их диаграмму на портале, доступ к ним через REST API или выполнение запросов с помощью PowerShell или CLI. Полный список метрик платформы, доступных в настоящее время с помощью конвейера консолидированной метрики Azure Monitor, см. в статье [Поддерживаемые метрики](metrics-supported.md) . Чтобы запросить эти метрики и получить к ним доступ, используйте [версию api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий.

Вы можете экспортировать метрики платформы из конвейера Azure Monitor в другие расположения одним из двух способов.
1. Использование [параметров диагностики](diagnostic-settings.md) для отправки в log Analytics, концентраторы событий или службу хранилища Azure.
2. Использование [метрик REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Из-за тонкостей в Azure Monitor серверной части не все метрики могут быть экспортированы с помощью параметров диагностики. В таблице ниже перечислены параметры, которые можно и нельзя экспортировать с помощью параметров диагностики.

Экспортировать с помощью параметров диагностики? | ResourceType | Метрика | метрикдисплайнаме | Единица | AggregationType
|----|-----|------|----|----|-----|
Да | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Память: текущая цена очистки | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Память: несжимаемая память очистки | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Память: сжимаемая память очистки | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Потоки: занятые потоки в пуле команд | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Потоки: бездействующие потоки в пуле команд | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Длина очереди заданий пула команд | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | CurrentConnections | Подключение: текущие подключения | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | CurrentUserSessions | Текущие пользовательские сеансы | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Потоки: занятые потоки продолжительного анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Потоки: бездействующие потоки продолжительного анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Потоки: длина очереди заданий продолжительного анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | Память подсистемы M | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | Байт исключительного числа модулей M | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | QPU подсистемы M | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | Виртуальный байт модуля M | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | memory_metric | Память | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Пробуксовка памяти | Процент | Среднее
Да | Microsoft.AnalysisServices/servers | MemoryLimitHard | Память: твердый лимит памяти | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Память: верхний предел памяти | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | MemoryLimitLow | Память: нижний предел памяти | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Память: ограничение памяти VertiPaq | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | MemoryUsage | Память: использование памяти | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | private_bytes_metric | Байты исключительного пользования | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Потоки: занятые потоки ввода-вывода в пуле обработки заданий | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Потоки: длина очереди заданий ввода-вывода пула обработки | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Длина очереди заданий пула обработки | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Занятые потоки в пуле запросов | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Потоки: бездействующие потоки в пуле запросов | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Потоки: длина очереди заданий пула запросов | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | Квота | Память: квота | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | QuotaBlocked | Память: заблокировано квот | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Обработка: преобразовано строк в секунду | Число/с | Среднее
Да | Microsoft.AnalysisServices/servers | RowsReadPerSec | Обработка: считано строк в секунду | Число/с | Среднее
Да | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Обработка: записано строк в секунду | Число/с | Среднее
Да | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Потоки: занятые потоки быстрого анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Потоки: бездействующие потоки быстрого анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Потоки: длина очереди заданий быстрого анализа | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Число успешных подключений в секунду | Число/с | Среднее
Да | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Общее число неудачных подключений | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Общее число запросов на подключение | Количество | Среднее
Да | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Память: размер нестраничных данных VertiPaq | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | VertiPaqPaged | Память: размер страничных данных VertiPaq | Байты | Среднее
Да | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Байт виртуальной памяти | Байты | Среднее
Да | Microsoft.ApiManagement/service | баккенддуратион | Длительность внутренних запросов | Миллисекунды | Среднее
Да | Microsoft.ApiManagement/service | Ориентированное на объем | Ориентированное на объем | Процент | Среднее
Да | Microsoft.ApiManagement/service | Длительность | Общая длительность запросов шлюза | Миллисекунды | Среднее
Да | Microsoft.ApiManagement/service | евенсубдроппедевентс | Удалены события EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубрежектедевентс | Отклоненные события EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубсукцессфулевентс | Успешные события EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубсроттледевентс | Регулируемые события EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубтимедаутевентс | Истекло время ожидания событий EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубтоталбитессент | Размер событий EventHub | Байты | Итого
Да | Microsoft.ApiManagement/service | евенсубтоталевентс | Всего событий EventHub | Количество | Итого
Да | Microsoft.ApiManagement/service | евенсубтоталфаиледевентс | События EventHub, завершившиеся сбоем | Количество | Итого
Да | Microsoft.ApiManagement/service | FailedRequests | Неудачные запросы к шлюзу (не рекомендуется) | Количество | Итого
Да | Microsoft.ApiManagement/service | OtherRequests | Другие запросы к шлюзу (не рекомендуется) | Количество | Итого
Да | Microsoft.ApiManagement/service | Запросы | Запросы | Количество | Итого
Да | Microsoft.ApiManagement/service | SuccessfulRequests | Успешные запросы к шлюзу (не рекомендуется) | Количество | Итого
Да | Microsoft.ApiManagement/service | TotalRequests | Всего запросов к шлюзу (устарело) | Количество | Итого
Да | Microsoft.ApiManagement/service | UnauthorizedRequests | Неавторизованные запросы к шлюзу (устарели) | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | аппкпуусажеперцентаже | Процент использования ЦП приложением | Процент | Среднее
Да | Microsoft. Аппплатформ/пружина | аппмеморикоммиттед | Назначенная память приложения | Байты | Среднее
Да | Microsoft. Аппплатформ/пружина | аппмеморимакс | Максимальная память приложения | Байты | Максимальная
Да | Microsoft. Аппплатформ/пружина | аппмеморюсед | Используемая память приложения | Байты | Среднее
Да | Microsoft. Аппплатформ/пружина | гкпаусетоталкаунт | Счетчик приостановки GC | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | гкпаусетоталтиме | Общее время приостановки сборки мусора | Миллисекунды | Итого
Да | Microsoft. Аппплатформ/пружина | максолдженмеморипулбитес | Максимальный доступный размер данных старого поколения | Байты | Среднее
Да | Microsoft. Аппплатформ/пружина | олдженмеморипулбитес | Размер данных старого поколения | Байты | Среднее
Да | Microsoft. Аппплатформ/пружина | олдженпромотедбитес | Повысить до старого размера данных поколения | Байты | Максимальная
Да | Microsoft. Аппплатформ/пружина | системкпуусажеперцентаже | Процент использования процессора системой | Процент | Среднее
Да | Microsoft. Аппплатформ/пружина | томкатерроркаунт | Глобальная ошибка Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатрецеиведбитес | Всего получено байт в Tomcat | Байты | Итого
Да | Microsoft. Аппплатформ/пружина | томкатрекуестмакстиме | Максимальное время запроса Tomcat | Миллисекунды | Максимальная
Да | Microsoft. Аппплатформ/пружина | томкатрекуесттоталкаунт | Общее число запросов Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатрекуесттоталтиме | Общее время запроса Tomcat | Миллисекунды | Итого
Да | Microsoft. Аппплатформ/пружина | томкатреспонсеавгтиме | Среднее время запроса Tomcat | Миллисекунды | Среднее
Да | Microsoft. Аппплатформ/пружина | томкатсентбитес | Всего отправленных байтов Tomcat | Байты | Итого
Да | Microsoft. Аппплатформ/пружина | томкатсессионактивекурренткаунт | Счетчик активности сеансов Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатсессионактивемакскаунт | Максимальное число активных сеансов Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатсессионаливемакстиме | Максимальное время активности сеанса Tomcat | Миллисекунды | Максимальная
Да | Microsoft. Аппплатформ/пружина | томкатсессионкреатедкаунт | Число созданных сеансов Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатсессионекспиредкаунт | Счетчик с истекшим сроком действия сеанса Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | томкатсессионрежектедкаунт | Число отклоненных сеансов Tomcat | Количество | Итого
Да | Microsoft. Аппплатформ/пружина | йоунгженпромотедбитес | Превращение в размер данных создания Титов. | Байты | Максимальная
Да | Microsoft.Automation/automationAccounts | TotalJob | Всего заданий | Количество | Итого
Да | Microsoft.Automation/automationAccounts | тоталупдатедеплойментмачинерунс | Всего запусков компьютера для развертывания обновлений | Количество | Итого
Да | Microsoft.Automation/automationAccounts | тоталупдатедеплойментрунс | Всего запусков развертывания обновлений | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | CoreCount | Dedicated Core Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | CreatingNodeCount | Creating Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | IdleNodeCount | Idle Node Count | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Job Delete Complete Events (События окончания удаления задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Job Delete Start Events (События начала удаления задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Job Disable Complete Events (События окончания отключения задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Job Disable Start Events (События начала отключения задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobStartEvent | Job Start Events (События запуска задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Job Terminate Complete Events (События окончания завершения задания) | Количество | Итого
Да | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Job Terminate Start Events (События начала завершения задания) | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Leaving Pool Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority Core Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | OfflineNodeCount | Offline Node Count | Количество | Итого
Да | Microsoft.Batch/batchAccounts | PoolCreateEvent | Pool Create Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Pool Delete Complete Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Pool Delete Start Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Pool Resize Complete Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Pool Resize Start Events | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Preempted Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | RebootingNodeCount | Rebooting Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Reimaging Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | RunningNodeCount | Running Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | StartingNodeCount | Starting Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Start Task Failed Node Count | Количество | Итого
Да | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Task Complete Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | TaskFailEvent | Task Fail Events | Количество | Итого
Да | Microsoft.Batch/batchAccounts | TaskStartEvent | Task Start Events | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Low-Priority Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | TotalNodeCount | Dedicated Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | UnusableNodeCount | Unusable Node Count | Количество | Итого
Нет | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Waiting For Start Task Node Count | Количество | Итого
Да | Microsoft. BatchAI/рабочие области | Активные ядра | Активные ядра | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Активные узлы | Активные узлы | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Бездействующие ядра | Бездействующие ядра | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Бездействующие узлы | Бездействующие узлы | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Задание выполнено | Задание выполнено | Количество | Итого
Да | Microsoft. BatchAI/рабочие области | Задание Отправлено | Задание Отправлено | Количество | Итого
Да | Microsoft. BatchAI/рабочие области | Освобождение ядер | Освобождение ядер | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Покинуть узлы | Покинуть узлы | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Вытесненные ядра | Вытесненные ядра | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Прерванные узлы | Прерванные узлы | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Процент использования квоты | Процент использования квоты | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Всего ядер | Всего ядер | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Всего узлов | Всего узлов | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Непригодные для использования ядра | Непригодные для использования ядра | Количество | Среднее
Да | Microsoft. BatchAI/рабочие области | Неиспользуемые узлы | Неиспользуемые узлы | Количество | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | коннектионакцептед | Принятые подключения | Количество | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | коннектионактиве | Активные подключения | Количество | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | коннектионхандлед | Обработанные соединения | Количество | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | кпуусажеперцентажеиндаубле | Процент использования ЦП | Процент | Максимальная
Да | Microsoft. Блокчейн/Блоккчаинмемберс | иореадбитес | Считанные байты ввода-вывода | Байты | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | иовритебитес | Число операций записи ввода-вывода | Байты | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | MemoryLimit | Ограничение памяти | Байты | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | MemoryUsage | Использование памяти | Байты | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | меморюсажеперцентажеиндаубле | Процент использования памяти | Процент | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | пендингтрансактионс | Ожидающие транзакции | Количество | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | процесседблоккс | Обработанные блоки | Количество | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | процесседтрансактионс | Обработанные транзакции | Количество | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | куеуедтрансактионс | Транзакции в очереди | Количество | Среднее
Да | Microsoft. Блокчейн/Блоккчаинмемберс | рекуессандлед | Обработанные запросы | Количество | Итого
Да | Microsoft. Блокчейн/Блоккчаинмемберс | сторажеусаже | Использование хранилища | Байты | Среднее
Да | Microsoft.Cache/redis | cachehits | Попаданий в кэш | Количество | Итого
Да | Microsoft.Cache/redis | cachehits0 | Cache Hits (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits1 | Cache Hits (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits2 | Cache Hits (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits3 | Cache Hits (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits4 | Cache Hits (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits5 | Cache Hits (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits6 | Cache Hits (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits7 | Cache Hits (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits8 | Cache Hits (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | cachehits9 | Cache Hits (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | cacheLatency | Cache Latency Microseconds (Задержка кэша в микросекундах (предварительная версия)) | Количество | Среднее
Да | Microsoft.Cache/redis | cachemisses | Промахов в кэше | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses0 | Cache Misses (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses1 | Cache Misses (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses2 | Cache Misses (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses3 | Cache Misses (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses4 | Cache Misses (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses5 | Cache Misses (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses6 | Cache Misses (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses7 | Cache Misses (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses8 | Cache Misses (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | cachemisses9 | Cache Misses (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | cacheRead | Чтение из кэша | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead0 | Cache Read (Shard 0) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead1 | Cache Read (Shard 1) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead2 | Cache Read (Shard 2) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead3 | Cache Read (Shard 3) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead4 | Cache Read (Shard 4) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead5 | Cache Read (Shard 5) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead6 | Cache Read (Shard 6) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead7 | Cache Read (Shard 7) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead8 | Cache Read (Shard 8) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheRead9 | Cache Read (Shard 9) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite | Запись в кэш | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite0 | Cache Write (Shard 0) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite1 | Cache Write (Shard 1) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite2 | Cache Write (Shard 2) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite3 | Cache Write (Shard 3) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite4 | Cache Write (Shard 4) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite5 | Cache Write (Shard 5) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite6 | Cache Write (Shard 6) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite7 | Cache Write (Shard 7) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite8 | Cache Write (Shard 8) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | cacheWrite9 | Cache Write (Shard 9) | Байт/с | Максимальная
Да | Microsoft.Cache/redis | connectedclients | Подключенные клиенты | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients0 | Connected Clients (Shard 0) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients1 | Connected Clients (Shard 1) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients2 | Connected Clients (Shard 2) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients3 | Connected Clients (Shard 3) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients4 | Connected Clients (Shard 4) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients5 | Connected Clients (Shard 5) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients6 | Connected Clients (Shard 6) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients7 | Connected Clients (Shard 7) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients8 | Connected Clients (Shard 8) | Количество | Максимальная
Да | Microsoft.Cache/redis | connectedclients9 | Connected Clients (Shard 9) | Количество | Максимальная
Да | Microsoft.Cache/redis | ошибки | ошибки | Количество | Максимальная
Да | Microsoft.Cache/redis | evictedkeys | Исключенные ключи | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys0 | Evicted Keys (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys1 | Evicted Keys (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys2 | Evicted Keys (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys3 | Evicted Keys (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys4 | Evicted Keys (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys5 | Evicted Keys (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys6 | Evicted Keys (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys7 | Evicted Keys (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys8 | Evicted Keys (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | evictedkeys9 | Evicted Keys (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys | Ключи с истекшим сроком действия | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys0 | Expired Keys (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys1 | Expired Keys (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys2 | Expired Keys (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys3 | Expired Keys (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys4 | Expired Keys (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys5 | Expired Keys (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys6 | Expired Keys (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys7 | Expired Keys (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys8 | Expired Keys (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | expiredkeys9 | Expired Keys (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands | Операций считывания | Количество | Итого
Да | Microsoft.Cache/redis | getcommands0 | Gets (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands1 | Gets (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands2 | Gets (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands3 | Gets (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands4 | Gets (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands5 | Gets (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands6 | Gets (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands7 | Gets (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands8 | Gets (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands9 | Gets (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | operationsPerSecond | Количество операций в секунду | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond0 | Количество операций в секунду (сегмент 0). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond1 | Количество операций в секунду (сегмент 1). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond2 | Количество операций в секунду (сегмент 2). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond3 | Количество операций в секунду (сегмент 3). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond4 | Количество операций в секунду (сегмент 4). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond5 | Количество операций в секунду (сегмент 5). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond6 | Количество операций в секунду (сегмент 6). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond7 | Количество операций в секунду (сегмент 7). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond8 | Количество операций в секунду (сегмент 8). | Количество | Максимальная
Да | Microsoft.Cache/redis | operationsPerSecond9 | Количество операций в секунду (сегмент 9). | Количество | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime | ЦП | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime0 | CPU (Shard 0) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime1 | CPU (Shard 1) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime2 | CPU (Shard 2) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime3 | CPU (Shard 3) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime4 | CPU (Shard 4) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime5 | CPU (Shard 5) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime6 | CPU (Shard 6) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime7 | CPU (Shard 7) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime8 | CPU (Shard 8) | Процент | Максимальная
Да | Microsoft.Cache/redis | percentProcessorTime9 | CPU (Shard 9) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad | Загрузка сервера | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad0 | Server Load (Shard 0) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad1 | Server Load (Shard 1) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad2 | Server Load (Shard 2) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad3 | Server Load (Shard 3) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad4 | Server Load (Shard 4) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad5 | Server Load (Shard 5) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad6 | Server Load (Shard 6) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad7 | Server Load (Shard 7) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad8 | Server Load (Shard 8) | Процент | Максимальная
Да | Microsoft.Cache/redis | serverLoad9 | Server Load (Shard 9) | Процент | Максимальная
Да | Microsoft.Cache/redis | setcommands | Наборы | Количество | Итого
Да | Microsoft.Cache/redis | setcommands0 | Sets (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands1 | Sets (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands2 | Sets (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands3 | Sets (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands4 | Sets (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | getcommands5 | Sets (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands6 | Sets (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands7 | Sets (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands8 | Sets (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | setcommands9 | Sets (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed | Всего операций | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed0 | Total Operations (Shard 0) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed1 | Total Operations (Shard 1) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed2 | Total Operations (Shard 2) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed3 | Total Operations (Shard 3) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed4 | Total Operations (Shard 4) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed5 | Total Operations (Shard 5) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed6 | Total Operations (Shard 6) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed7 | Total Operations (Shard 7) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed8 | Total Operations (Shard 8) | Количество | Итого
Да | Microsoft.Cache/redis | totalcommandsprocessed9 | Total Operations (Shard 9) | Количество | Итого
Да | Microsoft.Cache/redis | totalkeys | Всего ключей | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys0 | Total Keys (Shard 0) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys1 | Total Keys (Shard 1) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys2 | Total Keys (Shard 2) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys3 | Total Keys (Shard 3) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys4 | Total Keys (Shard 4) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys5 | Total Keys (Shard 5) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys6 | Total Keys (Shard 6) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys7 | Total Keys (Shard 7) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys8 | Total Keys (Shard 8) | Количество | Максимальная
Да | Microsoft.Cache/redis | totalkeys9 | Total Keys (Shard 9) | Количество | Максимальная
Да | Microsoft.Cache/redis | usedmemory | Используемая память | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory0 | Used Memory (Shard 0) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory1 | Used Memory (Shard 1) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory2 | Used Memory (Shard 2) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory3 | Used Memory (Shard 3) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory4 | Used Memory (Shard 4) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory5 | Used Memory (Shard 5) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory6 | Used Memory (Shard 6) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory7 | Used Memory (Shard 7) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory8 | Used Memory (Shard 8) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemory9 | Used Memory (Shard 9) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemorypercentage | Процент используемой памяти | Процент | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss | RSS используемой памяти | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss0 | Used Memory RSS (Shard 0) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss1 | Used Memory RSS (Shard 1) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss2 | Used Memory RSS (Shard 2) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss3 | Used Memory RSS (Shard 3) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss4 | Used Memory RSS (Shard 4) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss5 | Used Memory RSS (Shard 5) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss6 | Used Memory RSS (Shard 6) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss7 | Used Memory RSS (Shard 7) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss8 | Used Memory RSS (Shard 8) | Байты | Максимальная
Да | Microsoft.Cache/redis | usedmemoryRss9 | Used Memory RSS (Shard 9) | Байты | Максимальная
Нет | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Bytes/Sec | Скорость чтения с диска | Байт/с | Среднее
Да | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Нет | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Bytes/Sec | Запись на диск | Байт/с | Среднее
Да | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft.ClassicCompute/domainNames/slots/roles | Сеть (входящий трафик) | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft.ClassicCompute/domainNames/slots/roles | Сеть (исходящий трафик) | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft.ClassicCompute/domainNames/slots/roles | Percentage CPU | Percentage CPU | Процент | Среднее
Нет | Microsoft.classicСompute/virtualMachines | Disk Read Bytes/Sec | Скорость чтения с диска | Байт/с | Среднее
Да | Microsoft.classicСompute/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Нет | Microsoft.classicСompute/virtualMachines | Disk Write Bytes/Sec | Запись на диск | Байт/с | Среднее
Да | Microsoft.classicСompute/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft.classicСompute/virtualMachines | Сеть (входящий трафик) | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft.classicСompute/virtualMachines | Сеть (исходящий трафик) | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft.classicСompute/virtualMachines | Percentage CPU | Percentage CPU | Процент | Среднее
Да | Microsoft.ClassicStorage/storageAccounts | Доступность | Доступность | Процент | Среднее
Да | Microsoft.ClassicStorage/storageAccounts | Исходящие | Исходящие | Байты | Итого
Да | Microsoft.ClassicStorage/storageAccounts | Входящие | Входящие | Байты | Итого
Да | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.ClassicStorage/storageAccounts | Транзакции | Транзакции | Количество | Итого
Нет | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | Используемая емкость | Байты | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | Доступность | Доступность | Процент | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Блобсервицес | BlobCapacity | Емкость больших двоичных объектов | Байты | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Блобсервицес | BlobCount | Количество больших двоичных объектов | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | ContainerCount | Количество контейнеров больших двоичных объектов | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | Исходящие | Исходящие | Байты | Итого
Нет | Microsoft. Классикстораже/storageAccounts/Блобсервицес | IndexCapacity | Емкость индекса | Байты | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | Входящие | Входящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Блобсервицес | Транзакции | Транзакции | Количество | Итого
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | Доступность | Доступность | Процент | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | Исходящие | Исходящие | Байты | Итого
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | FileCapacity | Емкость файла | Байты | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | FileCount | Количество файлов | Количество | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | FileShareCount | Количество общих файловых ресурсов | Количество | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | филешарекуота | Размер квоты общей папки | Байты | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | филешареснапшоткаунт | Число моментальных снимков общей папки | Количество | Среднее
Нет | Microsoft. Классикстораже/storageAccounts/Филесервицес | филешареснапшотсизе | Размер моментального снимка файлового ресурса | Байты | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | Входящие | Входящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Филесервицес | Транзакции | Транзакции | Количество | Итого
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | Доступность | Доступность | Процент | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | Исходящие | Исходящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | Входящие | Входящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | QueueCapacity | Емкость очереди | Байты | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | QueueCount | Количество очередей | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | QueueMessageCount | Количество сообщений очереди | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Куеуесервицес | Транзакции | Транзакции | Количество | Итого
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | Доступность | Доступность | Процент | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | Исходящие | Исходящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | Входящие | Входящие | Байты | Итого
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | TableCapacity | Емкость таблицы | Байты | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | TableCount | Количество таблиц | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | TableEntityCount | Количество сущностей таблиц | Количество | Среднее
Да | Microsoft. Классикстораже/storageAccounts/Таблесервицес | Транзакции | Транзакции | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | BlockedCalls | Blocked Calls | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | чарактерстраинед | Обученные символы | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | CharactersTranslated | Преобразованные символы | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | ClientErrors | Client Errors | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | DataIn | Входящие данные: | Байты | Итого
Да | Microsoft.CognitiveServices/accounts | DataOut | Выходные данные | Байты | Итого
Да | Microsoft.CognitiveServices/accounts | Задержка | Задержка | MilliSeconds | Среднее
Да | Microsoft.CognitiveServices/accounts | ServerErrors | Server Errors | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Длительность речи | Секунды | Итого
Да | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Successful Calls | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | TotalCalls | Total Calls | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | TotalErrors | Total Errors | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Всего вызовов токенов | Количество | Итого
Да | Microsoft.CognitiveServices/accounts | TotalTransactions | Всего транзакций | Количество | Итого
Да | Microsoft.Compute/virtualMachines | Использованные кредиты ЦП | Использованные кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Оставшиеся кредиты ЦП | Оставшиеся кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Длина очереди диска данных | Глубина очереди диска данных (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения с диска данных (байт/с) | Скорость чтения с диска данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Операций чтения с диска данных/с | Скорость чтения с диска данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи на диск данных (байт/с) | Скорость записи на диск данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Операций записи на диск данных в секунду | Скорость записи на диск данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения с диска | Скорость чтения с диска | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Disk Write Bytes | Disk Write Bytes | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Входящие потоки | Входящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Максимальная скорость создания входящих потоков | Максимальная скорость создания входящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Сеть (входящий трафик) | Сеть в оплатах (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Всего сети | Всего сети | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Сеть (исходящий трафик) | Оплата за сеть (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Общее количество сетевых исходящих | Общее количество сетевых исходящих | Байты | Итого
Да | Microsoft.Compute/virtualMachines | Длина очереди диска ОС | Глубина очереди диска ОС (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Операций чтения с диска ОС в секунду | Скорость чтения с диска ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Операций записи на диск ОС в секунду | Скорость записи на диск ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Длина очереди на диск ОС | Диск ОС длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения с диска ОС (операций/с) | Операций чтения с диска ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи на диск ОС (операций/с) | Операций записи на диск ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Исходящие потоки | Исходящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Максимальная скорость создания исходящих потоков | Максимальная скорость создания исходящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Длина очереди в расчете на диск | Диск данных длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения в расчете на диск (байт/с) | Скорость чтения с диска данных (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость чтения в расчете на диск (операций/с) | Операций чтения с диска данных/с (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи в расчете на диск (байт/с) | Скорость записи на диск данных (байт/с) (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Скорость записи в расчете на диск (операций/с) | Операций записи на диск данных в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachines | Percentage CPU | Percentage CPU | Процент | Среднее
Да | Microsoft.Compute/virtualMachines | Попадание в кэш диска данных Premium | Попадание на чтение кэша диска данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachines | Промах чтения кэша на диске данных Premium | Промах чтения кэша на диске данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachines | Попадание чтения кэша на диске ОС Premium | Попадание чтения кэша на диске ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachines | Промах чтения кэша на диске ОС Premium | Промах чтения кэша диска ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Использованные кредиты ЦП | Использованные кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Оставшиеся кредиты ЦП | Оставшиеся кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Длина очереди диска данных | Глубина очереди диска данных (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения с диска данных (байт/с) | Скорость чтения с диска данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Операций чтения с диска данных/с | Скорость чтения с диска данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи на диск данных (байт/с) | Скорость записи на диск данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Операций записи на диск данных в секунду | Скорость записи на диск данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения с диска | Скорость чтения с диска | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Disk Write Bytes | Disk Write Bytes | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Входящие потоки | Входящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Максимальная скорость создания входящих потоков | Максимальная скорость создания входящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Сеть (входящий трафик) | Сеть в оплатах (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Всего сети | Всего сети | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Сеть (исходящий трафик) | Оплата за сеть (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Общее количество сетевых исходящих | Общее количество сетевых исходящих | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets | Длина очереди диска ОС | Глубина очереди диска ОС (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Операций чтения с диска ОС в секунду | Скорость чтения с диска ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Операций записи на диск ОС в секунду | Скорость записи на диск ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Длина очереди на диск ОС | Диск ОС длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения с диска ОС (операций/с) | Операций чтения с диска ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи на диск ОС (операций/с) | Операций записи на диск ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Исходящие потоки | Исходящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Максимальная скорость создания исходящих потоков | Максимальная скорость создания исходящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Длина очереди в расчете на диск | Диск данных длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения в расчете на диск (байт/с) | Скорость чтения с диска данных (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость чтения в расчете на диск (операций/с) | Операций чтения с диска данных/с (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи в расчете на диск (байт/с) | Скорость записи на диск данных (байт/с) (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Скорость записи в расчете на диск (операций/с) | Операций записи на диск данных в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Percentage CPU | Percentage CPU | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Попадание в кэш диска данных Premium | Попадание на чтение кэша диска данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Промах чтения кэша на диске данных Premium | Промах чтения кэша на диске данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Попадание чтения кэша на диске ОС Premium | Попадание чтения кэша на диске ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets | Промах чтения кэша на диске ОС Premium | Промах чтения кэша диска ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Использованные кредиты ЦП | Использованные кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Оставшиеся кредиты ЦП | Оставшиеся кредиты ЦП | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Длина очереди диска данных | Глубина очереди диска данных (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения с диска данных (байт/с) | Скорость чтения с диска данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Операций чтения с диска данных/с | Скорость чтения с диска данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи на диск данных (байт/с) | Скорость записи на диск данных (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Операций записи на диск данных в секунду | Скорость записи на диск данных (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения с диска | Скорость чтения с диска | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Write Bytes | Disk Write Bytes | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Входящие потоки | Входящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Максимальная скорость создания входящих потоков | Максимальная скорость создания входящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Сеть (входящий трафик) | Сеть в оплатах (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Всего сети | Всего сети | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Сеть (исходящий трафик) | Оплата за сеть (не рекомендуется) | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Общее количество сетевых исходящих | Общее количество сетевых исходящих | Байты | Итого
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Длина очереди диска ОС | Глубина очереди диска ОС (Предварительная версия) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Операций чтения с диска ОС в секунду | Скорость чтения с диска ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Операций записи на диск ОС в секунду | Скорость записи на диск ОС (операций/с) (предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Длина очереди на диск ОС | Диск ОС длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения с диска ОС (байт/с) | Скорость чтения с диска ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения с диска ОС (операций/с) | Операций чтения с диска ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи на диск ОС (байт/с) | Скорость записи на диск ОС (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи на диск ОС (операций/с) | Операций записи на диск ОС в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Исходящие потоки | Исходящие потоки | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Максимальная скорость создания исходящих потоков | Максимальная скорость создания исходящих потоков (Предварительная версия) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Длина очереди в расчете на диск | Диск данных длина очереди (не рекомендуется) | Количество | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения в расчете на диск (байт/с) | Скорость чтения с диска данных (байт/с) (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость чтения в расчете на диск (операций/с) | Операций чтения с диска данных/с (устарело) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи в расчете на диск (байт/с) | Скорость записи на диск данных (байт/с) (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Скорость записи в расчете на диск (операций/с) | Операций записи на диск данных в секунду (не рекомендуется) | Число/с | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Percentage CPU | Percentage CPU | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Попадание в кэш диска данных Premium | Попадание на чтение кэша диска данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Промах чтения кэша на диске данных Premium | Промах чтения кэша на диске данных Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Попадание чтения кэша на диске ОС Premium | Попадание чтения кэша на диске ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Промах чтения кэша на диске ОС Premium | Промах чтения кэша диска ОС Premium (Предварительная версия) | Процент | Среднее
Да | Microsoft.ContainerInstance/containerGroups | CpuUsage | Загрузка ЦП | Количество | Среднее
Да | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Использование памяти | Байты | Среднее
Да | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Получено байтов по сети в секунду | Байты | Среднее
Да | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Передано байт по сети в секунду | Байты | Среднее
Да | Microsoft.ContainerRegistry/registries | рундуратион | Длительность выполнения | Миллисекунды | Итого
Да | Microsoft.ContainerRegistry/registries | сукцессфулпуллкаунт | Число успешных операций извлечения | Количество | Среднее
Да | Microsoft.ContainerRegistry/registries | сукцессфулпушкаунт | Число успешных push-уведомлений | Количество | Среднее
Да | Microsoft.ContainerRegistry/registries | тоталпуллкаунт | Общее число броских | Количество | Среднее
Да | Microsoft.ContainerRegistry/registries | тоталпушкаунт | Общее число push-уведомлений | Количество | Среднее
Нет | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Общее количество доступных ядер ЦП в управляемом кластере | Количество | Среднее
Нет | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Общий объем доступной памяти в управляемом кластере | Байты | Среднее
Нет | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Состояния для различных условий узла | Количество | Среднее
Нет | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Число модулей pod по фазам | Количество | Среднее
Нет | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Число модулей pod в состоянии готовности | Количество | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | AvailableCapacity | Доступная емкость | Байты | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | битесуплоадедтоклауд | Отправлено облачных байт (устройство) | Байты | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | битесуплоадедтоклаудпершаре | Отправлено облачных байт (общий ресурс) | Байты | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | клаудреадсраугхпут | Пропускная способность скачивания в облаке | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | клаудреадсраугхпутпершаре | Пропускная способность скачивания в облаке (общая папка) | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | клаудуплоадсраугхпут | Пропускная способность передачи в облаке | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | клаудуплоадсраугхпутпершаре | Пропускная способность передачи в облаке (общая папка) | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | хипервмеморютилизатион | Использование памяти при вычислении граничных ресурсов | Процент | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | хиперввиртуалпроцессорутилизатион | Вычисление ребра — процент ЦП | Процент | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | никреадсраугхпут | Пропускная способность чтения (сеть) | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | никвритесраугхпут | Пропускная способность записи (сеть) | Байт/с | Среднее
Да | Microsoft. Датабокседже/Датабокседжедевицес | Общая емкость составляет | Общая емкость | Байты | Среднее
Да | Microsoft.DataFactory/datafactories | FailedRuns и | циклы выполнения со сбоем; | Количество | Итого
Да | Microsoft.DataFactory/datafactories | SuccessfulRuns. | успешные циклы выполнения. | Количество | Итого
Да | Microsoft.DataFactory/factories; | активитиканцелледрунс | Отмененные метрики выполнения действий | Количество | Итого
Да | Microsoft.DataFactory/factories; | ActivityFailedRuns | Метрики неудачных выполнений действий. | Количество | Итого
Да | Microsoft.DataFactory/factories; | ActivitySucceededRuns | Метрики успешных выполнений действий. | Количество | Итого
Да | Microsoft.DataFactory/factories; | факторисизеингбунитс | Общий размер фабрики (ГБ единиц) | Количество | Максимальная
Да | Microsoft.DataFactory/factories; | IntegrationRuntimeAvailableMemory | Доступная память для среды выполнения интеграции | Байты | Среднее
Да | Microsoft.DataFactory/factories; | интегратионрунтимеаверажетаскпиккупделай | Длительность очереди среды выполнения интеграции | Секунды | Среднее
Да | Microsoft.DataFactory/factories; | IntegrationRuntimeCpuPercentage | Использование ЦП средой выполнения интеграции | Процент | Среднее
Да | Microsoft.DataFactory/factories; | интегратионрунтимекуеуеленгс | Длина очереди среды выполнения интеграции | Количество | Среднее
Да | Microsoft.DataFactory/factories; | максалловедфакторисизеингбунитс | Максимально допустимый размер фабрики (единица GB) | Количество | Максимальная
Да | Microsoft.DataFactory/factories; | максалловедресаурцекаунт | Максимальное число допустимых сущностей | Количество | Максимальная
Да | Microsoft.DataFactory/factories; | пипелинеканцелледрунс | Отмененные метрики запуска конвейера | Количество | Итого
Да | Microsoft.DataFactory/factories; | PipelineFailedRuns | Метрики неудачных выполнений конвейеров. | Количество | Итого
Да | Microsoft.DataFactory/factories; | PipelineSucceededRuns | Метрики успешных выполнений конвейеров. | Количество | Итого
Да | Microsoft.DataFactory/factories; | ресаурцекаунт | Общее число сущностей | Количество | Максимальная
Да | Microsoft.DataFactory/factories; | тригжерканцелледрунс | Отмененные метрики запуска триггера | Количество | Итого
Да | Microsoft.DataFactory/factories; | TriggerFailedRuns | Метрики неудачных запусков триггеров. | Количество | Итого
Да | Microsoft.DataFactory/factories; | TriggerSucceededRuns | Метрики успешных запусков триггеров. | Количество | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Cancelled AU Time | Секунды | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Failed AU Time | Секунды | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Successful AU Time | Секунды | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Cancelled Jobs | Количество | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Failed Jobs | Количество | Итого
Да | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Successful Jobs | Количество | Итого
Да | Microsoft.DataLakeStore/accounts | DataRead | Данных прочитано | Байты | Итого
Да | Microsoft.DataLakeStore/accounts | DataWritten | Записанные данные | Байты | Итого
Да | Microsoft.DataLakeStore/accounts | ReadRequests | Запросы на чтение | Количество | Итого
Да | Microsoft.DataLakeStore/accounts | TotalStorage | Общий объем хранилища | Байты | Максимальная
Да | Microsoft.DataLakeStore/accounts | WriteRequests | Запросы на запись | Количество | Итого
Да | Microsoft.DBforMariaDB/servers | active_connections | Активные подключения | Количество | Среднее
Да | Microsoft.DBforMariaDB/servers | backup_storage_used | Используемое хранилище резервных копий | Байты | Среднее
Да | Microsoft.DBforMariaDB/servers | connections_failed | Неудачные подключения | Количество | Итого
Да | Microsoft.DBforMariaDB/servers | cpu_percent | Нагрузка ЦП | Процент | Среднее
Да | Microsoft.DBforMariaDB/servers | io_consumption_percent | Процент ввода-вывода данных | Процент | Среднее
Да | Microsoft.DBforMariaDB/servers | memory_percent | Процент памяти | Процент | Среднее
Да | Microsoft.DBforMariaDB/servers | network_bytes_egress | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft.DBforMariaDB/servers | seconds_behind_master | Задержка репликации в секундах | Количество | Максимальная
Да | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Максимальный объем хранилища для журнала сервера | Байты | Среднее
Да | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Процент хранилища для журнала сервера | Процент | Среднее
Да | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Используемый объем хранилища для журнала сервера | Байты | Среднее
Да | Microsoft.DBforMariaDB/servers | storage_limit | Storage limit | Байты | Максимальная
Да | Microsoft.DBforMariaDB/servers | storage_percent | Процент хранилища | Процент | Среднее
Да | Microsoft.DBforMariaDB/servers | storage_used | Используемое хранилище | Байты | Среднее
Да | Microsoft.DBforMySQL/servers | active_connections | Активные подключения | Количество | Среднее
Да | Microsoft.DBforMySQL/servers | backup_storage_used | Используемое хранилище резервных копий | Байты | Среднее
Да | Microsoft.DBforMySQL/servers | connections_failed | Неудачные подключения | Количество | Итого
Да | Microsoft.DBforMySQL/servers | cpu_percent | Нагрузка ЦП | Процент | Среднее
Да | Microsoft.DBforMySQL/servers | io_consumption_percent | Процент ввода-вывода данных | Процент | Среднее
Да | Microsoft.DBforMySQL/servers | memory_percent | Процент памяти | Процент | Среднее
Да | Microsoft.DBforMySQL/servers | network_bytes_egress | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft.DBforMySQL/servers | network_bytes_ingress | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft.DBforMySQL/servers | seconds_behind_master | Задержка репликации в секундах | Количество | Максимальная
Да | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Максимальный объем хранилища для журнала сервера | Байты | Максимальная
Да | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Процент хранилища для журнала сервера | Процент | Среднее
Да | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Используемый объем хранилища для журнала сервера | Байты | Среднее
Да | Microsoft.DBforMySQL/servers | storage_limit | Storage limit | Байты | Максимальная
Да | Microsoft.DBforMySQL/servers | storage_percent | Процент хранилища | Процент | Среднее
Да | Microsoft.DBforMySQL/servers | storage_used | Используемое хранилище | Байты | Среднее
Да | Microsoft.DBforPostgreSQL/servers | active_connections | Активные подключения | Количество | Среднее
Да | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Используемое хранилище резервных копий | Байты | Среднее
Да | Microsoft.DBforPostgreSQL/servers | connections_failed | Неудачные подключения | Количество | Итого
Да | Microsoft.DBforPostgreSQL/servers | cpu_percent | Нагрузка ЦП | Процент | Среднее
Да | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | Процент ввода-вывода данных | Процент | Среднее
Да | Microsoft.DBforPostgreSQL/servers | memory_percent | Процент памяти | Процент | Среднее
Да | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Максимальная задержка между репликами | Байты | Максимальная
Да | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Задержка реплики | Секунды | Максимальная
Да | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Максимальный объем хранилища для журнала сервера | Байты | Максимальная
Да | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Процент хранилища для журнала сервера | Процент | Среднее
Да | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Используемый объем хранилища для журнала сервера | Байты | Среднее
Да | Microsoft.DBforPostgreSQL/servers | storage_limit | Storage limit | Байты | Максимальная
Да | Microsoft.DBforPostgreSQL/servers | storage_percent | Процент хранилища | Процент | Среднее
Да | Microsoft.DBforPostgreSQL/servers | storage_used | Используемое хранилище | Байты | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | active_connections | Активные подключения | Количество | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | cpu_percent | Нагрузка ЦП | Процент | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | / | IOPS | Количество | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | memory_percent | Процент памяти | Процент | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | network_bytes_egress | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft. Дбфорпостгрескл/serversv2 | network_bytes_ingress | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft. Дбфорпостгрескл/serversv2 | storage_percent | Процент хранилища | Процент | Среднее
Да | Microsoft. Дбфорпостгрескл/serversv2 | storage_used | Используемое хранилище | Байты | Среднее
Да | Microsoft. Devices/Account | дигиталтвинс. телеметрии. nodes | Заполнитель данных телеметрии для узла Digital двойников | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | Отброшенных сообщений C2D | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | Доставка сообщений C2D завершена | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Отклоненные сообщения C2D | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.methods.failure | Неудачные вызовы прямых методов. | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.methods.requestSize | Размер запроса вызовов прямых методов. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | c2d.methods.responseSize | Размер ответа вызовов прямых методов. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | c2d.methods.success | Успешные вызовы прямых методов. | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Неудачные операции чтения с двойников, инициированные из серверной части. | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.twin.read.size | Размер ответа операций чтения с двойников, инициированных из серверной части. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | c2d.twin.read.success | Успешные операции чтения с двойников, инициированные из серверной части. | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Неудачные обновления двойников, инициированные из серверной части. | Количество | Итого
Да | Microsoft.Devices/IotHubs | c2d.twin.update.size | Размер обновлений двойников, инициированных из серверной части. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | c2d.twin.update.success | Успешные обновления двойников, инициированные из серверной части. | Количество | Итого
Да | Microsoft.Devices/IotHubs | C2DMessagesExpired | Срок действия сообщений C2D истек (Предварительная версия) | Количество | Итого
Да | Microsoft.Devices/IotHubs | конфигурации | Configuration Metrics (Метрики конфигурации) | Количество | Итого
Нет | Microsoft.Devices/IotHubs | connectedDeviceCount | Connected devices (preview) (Подключенных устройств (предварительная версия)) | Количество | Среднее
Да | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Routing: messages delivered to messages/events (Маршрутизация: доставлено сообщений на конечную точку messages/events) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Routing: messages delivered to Event Hub (Маршрутизация: доставлено сообщений в концентратор событий) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Routing: messages delivered to Service Bus Queue (Маршрутизация: доставлено сообщений в очередь служебной шины) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Routing: messages delivered to Service Bus Topic (Маршрутизация: доставлено сообщений в раздел служебной шины) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.Endpoints.egress.Storage | Routing: messages delivered to storage (Маршрутизация: доставлено сообщений в хранилище) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.Endpoints.egress.Storage.BLOBs | Routing: blobs delivered to storage (Маршрутизация: доставлено BLOB-объектов в хранилище) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Routing: data delivered to storage (Маршрутизация: доставлено данных в хранилище) | Байты | Итого
Да | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Routing: message latency for messages/events (Маршрутизация: задержка сообщений для messages/events) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Routing: message latency for Event Hub (Маршрутизация: задержка сообщений для концентратора событий) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Routing: message latency for Service Bus Queue (Маршрутизация: задержка сообщений для очереди служебной шины) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Routing: message latency for Service Bus Topic (Маршрутизация: задержка сообщений для раздела служебной шины) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | d2c.Endpoints.latency.Storage | Routing: message latency for storage (Маршрутизация: задержка сообщений для хранилища) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Маршрутизация: сообщения телеметрии отброшены | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Routing: messages delivered to fallback (Маршрутизация: доставлено сообщений на резервный маршрут) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Routing: telemetry messages incompatible (Маршрутизация: несовместимых сообщений телеметрии) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Маршрутизация: сообщения телеметрии потеряны | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Routing: telemetry messages delivered (Маршрутизация: доставлено сообщений телеметрии) | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Число предпринятых попыток отправки сообщений телеметрии. | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Количество ошибок регулирования | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Число отправленных сообщений телеметрии. | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Неудачные операции чтения с двойников, инициированные устройством. | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.twin.read.size | Размер ответа операций чтения с двойников, инициированных устройством. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | d2c.twin.read.success | Успешные операции чтения с двойников, инициированные устройством. | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Неудачные обновления двойников, инициированные устройством. | Количество | Итого
Да | Microsoft.Devices/IotHubs | d2c.twin.update.size | Размер обновлений двойников, инициированных устройством. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | d2c.twin.update.success | Успешные обновления двойников, инициированные устройством. | Количество | Итого
Да | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Общее количество используемых сообщений | Количество | Среднее
Да | Microsoft.Devices/IotHubs | deviceDataUsage | Общее использование данных устройства | Байты | Итого
Да | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Total device data usage (preview) (Общий объем использования данных устройствами (предварительная версия)) | Байты | Итого
Да | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Connected devices (deprecated) (Подключенные устройства (не рекомендуется))  | Количество | Итого
Да | Microsoft.Devices/IotHubs | devices.totalDevices | Total devices (deprecated) (Всего устройств (не рекомендуется)) | Количество | Итого
Да | Microsoft.Devices/IotHubs | евентгридделивериес | Сетка событий: доставок (Предварительная версия) | Количество | Итого
Да | Microsoft.Devices/IotHubs | евентгридлатенци | Задержка сетки событий (Предварительная версия) | Миллисекунды | Среднее
Да | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Неудачные отмены заданий. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Успешные отмены заданий. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.completed | Завершенные задания | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Неудачные операции создания заданий вызова методов. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Успешные операции создания заданий вызова методов. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Неудачные операции создания заданий обновления двойников. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Успешные операции создания заданий обновления двойников. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.failed | Неудачные задания. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Неудачные вызовы получения списка заданий. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.listJobs.success | Успешные вызовы получения списка заданий. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Неудачные запросы заданий. | Количество | Итого
Да | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Успешные запросы заданий. | Количество | Итого
Нет | Microsoft.Devices/IotHubs | totalDeviceCount | Total devices (preview) (Всего устройств (предварительная версия)) | Количество | Среднее
Да | Microsoft.Devices/IotHubs | twinQueries.failure | Неудачные запросы двойников. | Количество | Итого
Да | Microsoft.Devices/IotHubs | twinQueries.resultSize | Размер результатов запросов двойников. | Байты | Среднее
Да | Microsoft.Devices/IotHubs | twinQueries.success | Успешные запросы двойников. | Количество | Итого
Да | Microsoft.Devices/provisioningServices | AttestationAttempts | Попытки аттестации | Количество | Итого
Да | Microsoft.Devices/provisioningServices | DeviceAssignments | Назначенные устройства | Количество | Итого
Да | Microsoft.Devices/provisioningServices | RegistrationAttempts | Попытки регистрации | Количество | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Доступная служба хранилища | Байты | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Отключение связи Cassandra | Количество | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Расходы запросов по Cassandra | Количество | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Запросы по Cassandra | Количество | Количество
Нет | Microsoft.DocumentDB/databaseAccounts | DataUsage | Использование данных | Байты | Итого
Да | Microsoft.DocumentDB/databaseAccounts | делетевиртуалнетворк | делетевиртуалнетворк | Количество | Количество
Нет | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Число документов | Количество | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Квота на документы | Байты | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Использование индексов | Байты | Итого
Нет | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | Запросы метаданных | Количество | Количество
Да | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Запросить оплату Mongo | Количество | Итого
Да | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Запросы Mongo | Количество | Количество
Нет | Microsoft.DocumentDB/databaseAccounts | монгорекуестскаунт | Частота запросов Mongo | Число/с | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | монгорекуестсделете | Частота запросов на удаление Mongo | Число/с | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | монгорекуестсинсерт | Частота запросов на вставку Mongo | Число/с | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | монгорекуестскуери | Частота запросов Mongo запросов | Число/с | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | монгорекуестсупдате | Частота запросов на обновление Mongo | Число/с | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Подготовленная пропускная способность | Количество | Максимальная
Да | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | Задержка репликации P99 | MilliSeconds | Среднее
Нет | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | Доступность службы | Процент | Среднее
Да | Microsoft.DocumentDB/databaseAccounts | TotalRequests | Общее количество запросов | Количество | Количество
Да | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Общее количество единиц запросов | Количество | Итого
Нет | Microsoft. Ентерприсекновледжеграф/Services | FailureCount | Число сбоев | Количество | Количество
Нет | Microsoft. Ентерприсекновледжеграф/Services | SuccessCount | Подсчет успешных событий | Количество | Количество
Нет | Microsoft. Ентерприсекновледжеграф/Services | сукцесслатенци | Задержка успешного выполнения | MilliSeconds | Среднее
Нет | Microsoft. Ентерприсекновледжеграф/Services | трансактионкаунт | Число транзакций | Количество | Количество
Да | Microsoft. EventGrid/Domains | DeadLetteredCount | Dead Lettered Events (Невостребованные события) | Количество | Итого
Нет | Microsoft. EventGrid/Domains | DeliveryAttemptFailCount | Delivery Failed Events (Недоставленные события) | Количество | Итого
Да | Microsoft. EventGrid/Domains | DeliverySuccessCount | Delivered Events (Доставленные события) | Количество | Итого
Нет | Microsoft. EventGrid/Domains | DestinationProcessingDurationInMs | Destination Processing Duration (Длительность обработки назначения) | Миллисекунды | Среднее
Да | Microsoft. EventGrid/Domains | DroppedEventCount | Удаленные события | Количество | Итого
Да | Microsoft. EventGrid/Domains | MatchedEventCount | Соответствующие события | Количество | Итого
Да | Microsoft. EventGrid/Domains | PublishFailCount | Опубликовать события с ошибками | Количество | Итого
Да | Microsoft. EventGrid/Domains | PublishSuccessCount | Published Events (Опубликованные события) | Количество | Итого
Да | Microsoft. EventGrid/Domains | публишсукцесслатенциинмс | Задержка успешной публикации | Количество | Итого
Да | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Dead Lettered Events (Невостребованные события) | Количество | Итого
Нет | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Delivery Failed Events (Недоставленные события) | Количество | Итого
Да | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Delivered Events (Доставленные события) | Количество | Итого
Нет | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Destination Processing Duration (Длительность обработки назначения) | Миллисекунды | Среднее
Да | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Удаленные события | Количество | Итого
Да | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Соответствующие события | Количество | Итого
Да | Microsoft.EventGrid/extensionTopics | PublishFailCount | Опубликовать события с ошибками | Количество | Итого
Да | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Published Events (Опубликованные события) | Количество | Итого
Да | Microsoft.EventGrid/extensionTopics | публишсукцесслатенциинмс | Задержка успешной публикации | Количество | Итого
Да | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Unmatched Events (Несоответствующие события) | Количество | Итого
Да | Microsoft.EventGrid/topics | PublishFailCount | Опубликовать события с ошибками | Количество | Итого
Да | Microsoft.EventGrid/topics | PublishSuccessCount | Published Events (Опубликованные события) | Количество | Итого
Да | Microsoft.EventGrid/topics | публишсукцесслатенциинмс | Задержка успешной публикации | Количество | Итого
Да | Microsoft.EventGrid/topics | UnmatchedEventCount | Unmatched Events (Несоответствующие события) | Количество | Итого
Нет | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | Количество | Среднее
Нет | Microsoft.EventHub/clusters | AvailableMemory | Доступная память | Процент | Максимальная
Нет | Microsoft.EventHub/clusters | CaptureBacklog | Невыполненная работа записи. | Количество | Итого
Нет | Microsoft.EventHub/clusters | CapturedBytes | Записанные байты. | Байты | Итого
Нет | Microsoft.EventHub/clusters | CapturedMessages | Записанные сообщения. | Количество | Итого
Нет | Microsoft.EventHub/clusters | ConnectionsOpened | Закрытые подключения. | Количество | Среднее
Нет | Microsoft.EventHub/clusters | ConnectionsOpened | Открытые подключения. | Количество | Среднее
Нет | Microsoft.EventHub/clusters | ЦП | ЦП | Процент | Максимальная
Да | Microsoft.EventHub/clusters | IncomingBytes | Входящие байты. | Байты | Итого
Да | Microsoft.EventHub/clusters | IncomingMessages | Входящие сообщения | Количество | Итого
Да | Microsoft.EventHub/clusters | IncomingRequests | Входящих запросов | Количество | Итого
Да | Microsoft.EventHub/clusters | OutgoingBytes | Исходящие байты. | Байты | Итого
Да | Microsoft.EventHub/clusters | OutgoingMessages | Исходящие сообщения | Количество | Итого
Нет | Microsoft.EventHub/clusters | QuotaExceededErrors | Ошибки превышения квоты. | Количество | Итого
Нет | Microsoft.EventHub/clusters | ServerErrors | Ошибки сервера. | Количество | Итого
Нет | Microsoft.EventHub/clusters | SuccessfulRequests | Выполненные запросы | Количество | Итого
Нет | Microsoft.EventHub/clusters | ThrottledRequests | Регулируемые запросы. | Количество | Итого
Нет | Microsoft.EventHub/clusters | UserErrors | Ошибки пользователей. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Количество | Среднее
Нет | Microsoft.EventHub/namespaces | CaptureBacklog | Невыполненная работа записи. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | CapturedBytes | Записанные байты. | Байты | Итого
Нет | Microsoft.EventHub/namespaces | CapturedMessages | Записанные сообщения. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | ConnectionsOpened | Закрытые подключения. | Количество | Среднее
Нет | Microsoft.EventHub/namespaces | ConnectionsOpened | Открытые подключения. | Количество | Среднее
Да | Microsoft.EventHub/namespaces | EHABL | Архивировать сообщения невыполненной работы (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | EHAMBS | Пропускная способность сообщений архивации (не рекомендуется) | Байты | Итого
Да | Microsoft.EventHub/namespaces | EHAMSGS | Архивирование сообщений (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | EHINBYTES | Входящие байты (не рекомендуется) | Байты | Итого
Да | Microsoft.EventHub/namespaces | EHINMBS | Входящие байты (устарели) (не рекомендуется) | Байты | Итого
Да | Microsoft.EventHub/namespaces | EHINMSGS | Входящие сообщения (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | EHOUTBYTES | Исходящие байты (не рекомендуется) | Байты | Итого
Да | Microsoft.EventHub/namespaces | EHOUTMBS | Исходящие байты (устарели) (не рекомендуется) | Байты | Итого
Да | Microsoft.EventHub/namespaces | EHOUTMSGS | Исходящие сообщения (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | FAILREQ | Неудачные запросы (устарело) | Количество | Итого
Да | Microsoft.EventHub/namespaces | IncomingBytes | Входящие байты. | Байты | Итого
Да | Microsoft.EventHub/namespaces | IncomingMessages | Входящие сообщения | Количество | Итого
Да | Microsoft.EventHub/namespaces | IncomingRequests | Входящих запросов | Количество | Итого
Да | Microsoft.EventHub/namespaces | INMSGS | Входящие сообщения (устаревшие) (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | INREQS | Входящие запросы (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | INTERR | Внутренние ошибки сервера (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | MISCERR | Другие ошибки (не рекомендуется) | Количество | Итого
Да | Microsoft.EventHub/namespaces | OutgoingBytes | Исходящие байты. | Байты | Итого
Да | Microsoft.EventHub/namespaces | OutgoingMessages | Исходящие сообщения | Количество | Итого
Да | Microsoft.EventHub/namespaces | OUTMSGS | Исходящие сообщения (устаревшие) (не рекомендуется) | Количество | Итого
Нет | Microsoft.EventHub/namespaces | QuotaExceededErrors | Ошибки превышения квоты. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | ServerErrors | Ошибки сервера. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | Размер | Размер | Байты | Среднее
Нет | Microsoft.EventHub/namespaces | SuccessfulRequests | Выполненные запросы | Количество | Итого
Да | Microsoft.EventHub/namespaces | SUCCREQ | Успешные запросы (устарело) | Количество | Итого
Да | Microsoft.EventHub/namespaces | SVRBSY | Ошибки занятости сервера (устарели) | Количество | Итого
Нет | Microsoft.EventHub/namespaces | ThrottledRequests | Регулируемые запросы. | Количество | Итого
Нет | Microsoft.EventHub/namespaces | UserErrors | Ошибки пользователей. | Количество | Итого
Да | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Запросы к шлюзу по категориям | Количество | Итого
Да | Microsoft.HDInsight/clusters | GatewayRequests | Запросы к шлюзу | Количество | Итого
Да | Microsoft.HDInsight/clusters | нумактивеворкерс | Число активных рабочих ролей | Количество | Максимальная
Да | Microsoft.HDInsight/clusters | скалингрекуестс | Запросы на масштабирование | Количество | Максимальная
Да | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Пороговое значение метрики | Количество | Среднее
Да | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Наблюдаемая емкость | Количество | Среднее
Да | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Наблюдаемое значение метрики | Количество | Среднее
Да | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Инициированные действия масштабирования | Количество | Итого
Да | Microsoft.Insights/Components | availabilityResults/Аваилабилитиперцентаже | Доступность | Процент | Среднее
Нет | Microsoft.Insights/Components | availabilityResults/Count | Тесты доступности | Количество | Количество
Да | Microsoft.Insights/Components | availabilityResults/duration | Длительность теста доступности | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | browserTimings/networkDuration | Время подключения к сети при загрузке страницы | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | browserTimings/processingDuration | Время обработки клиента | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | browserTimings/receiveDuration | Время получения ответа | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | browserTimings/sendDuration | Время отправки запроса | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | browserTimings/totalDuration | Время загрузки страницы в браузере | MilliSeconds | Среднее
Нет | Microsoft.Insights/Components | dependencies/count | Вызовы зависимостей | Количество | Количество
Да | Microsoft.Insights/Components | dependencies/duration | Длительность зависимости | MilliSeconds | Среднее
Нет | Microsoft.Insights/Components | dependencies/failed | Сбои при вызове зависимостей | Количество | Количество
Нет | Microsoft.Insights/Components | exceptions/browser | Исключения браузера | Количество | Количество
Да | Microsoft.Insights/Components | exceptions/count | Исключения | Количество | Количество
Нет | Microsoft.Insights/Components | exceptions/server | Исключения сервера | Количество | Количество
Да | Microsoft.Insights/Components | pageViews/count | Просмотры страниц | Количество | Количество
Да | Microsoft.Insights/Components | pageViews/duration | Время загрузки страницы для просмотра | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | Частота исключений | Число/с | Среднее
Да | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | Объем доступной памяти | Байты | Среднее
Да | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | Обработка ЦП | Процент | Среднее
Да | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | Скорость ввода-вывода процесса | Байт/с | Среднее
Да | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | Процессорное время | Процент | Среднее
Да | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | Количество байтов исключительного использования процессов | Байты | Среднее
Да | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | Время выполнения HTTP-запроса | MilliSeconds | Среднее
Да | Microsoft.Insights/Components | performanceCounters/requestsInQueue | HTTP requests in application queue (HTTP-запросы в очереди приложений) | Количество | Среднее
Да | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | Скорость HTTP-запроса | Число/с | Среднее
Нет | Microsoft.Insights/Components | requests/count | Запросы сервера | Количество | Количество
Да | Microsoft.Insights/Components | requests/duration | Время ответа от сервера | MilliSeconds | Среднее
Нет | Microsoft.Insights/Components | requests/failed | Failed requests (Неудачные запросы) | Количество | Количество
Нет | Microsoft.Insights/Components | запросов и ставок | Частота запросов сервера | Число/с | Среднее
Да | Microsoft.Insights/Components | traces/count | Трассировки | Количество | Количество
Да | Microsoft.KeyVault/vaults | ServiceApiHit | Всего попаданий в API службы | Количество | Количество
Да | Microsoft.KeyVault/vaults | ServiceApiLatency | Общая задержка API службы | Миллисекунды | Среднее
Да | Microsoft.KeyVault/vaults | ServiceApiResult | Всего результатов для API службы | Количество | Количество
Да | Microsoft.Kusto/Clusters | качеутилизатион | Использование кэша | Процент | Среднее
Да | Microsoft.Kusto/Clusters | континуаусекспортмакслатенессминутес | Максимальное время непрерывного экспорта (мин.) | Количество | Максимальная
Да | Microsoft.Kusto/Clusters | континуаусекспортнумофрекордсекспортед | Непрерывный экспорт-число экспортированных записей | Количество | Итого
Да | Microsoft.Kusto/Clusters | континуаусекспортпендингкаунт | Число ожидающих непрерывного экспорта | Количество | Максимальная
Да | Microsoft.Kusto/Clusters | континуаусекспортресулт | Результат непрерывного экспорта | Количество | Количество
Да | Microsoft.Kusto/Clusters | ЦП | ЦП | Процент | Среднее
Да | Microsoft.Kusto/Clusters | евентспроцесседфоревенсубс | Обработано событий (для концентраторов событий и IoT) | Количество | Итого
Да | Microsoft.Kusto/Clusters | експортутилизатион | Использование экспорта | Процент | Максимальная
Да | Microsoft.Kusto/Clusters | инжестионлатенциинсекондс | Задержка приема (в секундах) | Секунды | Среднее
Да | Microsoft.Kusto/Clusters | IngestionResult | Результат приема | Количество | Количество
Да | Microsoft.Kusto/Clusters | инжестионутилизатион | Использование приема | Процент | Среднее
Да | Microsoft.Kusto/Clusters | инжестионволумеинмб | Объем приема (в МБ) | Количество | Итого
Да | Microsoft.Kusto/Clusters | Проверки | Срок поддержания активности | Количество | Среднее
Да | Microsoft.Kusto/Clusters | QueryDuration | Query duration (Длительность запросов) | Миллисекунды | Среднее
Да | Microsoft.Kusto/Clusters | стеамингинжестрекуестрате | Частота запросов приема потоковой передачи | Количество | ратерекуестсперсеконд
Да | Microsoft.Kusto/Clusters | стреамингинжестдатарате | Скорость приема данных потоковой передачи | Количество | Среднее
Да | Microsoft.Kusto/Clusters | стреамингинжестдуратион | Длительность приема потоковой передачи | Миллисекунды | Среднее
Да | Microsoft.Kusto/Clusters | стреамингинжестресултс | Поток приема результатов | Количество | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionLatency | Action Latency  | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionsCompleted | Actions Completed  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionsFailed | Actions Failed  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionsSkipped | Actions Skipped  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionsStarted | Actions Started  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionsSucceeded | Actions Succeeded  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionSuccessLatency | Action Success Latency  | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | ActionThrottledEvents | Action Throttled Events | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | интегратионсервицеенвиронментконнектормеморюсаже | Использование памяти соединителя для среда службы интеграции | Процент | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | интегратионсервицеенвиронментконнекторпроцессорусаже | Использование процессора соединителя для среда службы интеграции | Процент | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | интегратионсервицеенвиронментворкфловмеморюсаже | Использование памяти рабочего процесса для среда службы интеграции | Процент | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | интегратионсервицеенвиронментворкфловпроцессорусаже | Использование процессора рабочих процессов для среда службы интеграции | Процент | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunFailurePercentage | Run Failure Percentage | Процент | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunLatency | Run Latency | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunsCancelled | Выполнение отменено | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunsCompleted | Runs Completed | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunsFailed | Runs Failed | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunsStarted | Runs Started | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunsSucceeded | Runs Succeeded | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | рунстартсроттледевентс | Запустить регулируемые события запуска | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunSuccessLatency | Run Success Latency | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | RunThrottledEvents | Run Throttled Events | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggerFireLatency | Trigger Fire Latency  | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggerLatency | Trigger Latency  | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersCompleted | Triggers Completed  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersFailed | Triggers Failed  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersFired | Triggers Fired  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersSkipped | Triggers Skipped | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersStarted | Triggers Started  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggersSucceeded | Triggers Succeeded  | Количество | Итого
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggerSuccessLatency | Trigger Success Latency  | Секунды | Среднее
Да | Microsoft. Logic/Интегратионсервицеенвиронментс | TriggerThrottledEvents | Trigger Throttled Events | Количество | Итого
Да | Microsoft.Logic/workflows | ActionLatency | Action Latency  | Секунды | Среднее
Да | Microsoft.Logic/workflows | ActionsCompleted | Actions Completed  | Количество | Итого
Да | Microsoft.Logic/workflows | ActionsFailed | Actions Failed  | Количество | Итого
Да | Microsoft.Logic/workflows | ActionsSkipped | Actions Skipped  | Количество | Итого
Да | Microsoft.Logic/workflows | ActionsStarted | Actions Started  | Количество | Итого
Да | Microsoft.Logic/workflows | ActionsSucceeded | Actions Succeeded  | Количество | Итого
Да | Microsoft.Logic/workflows | ActionSuccessLatency | Action Success Latency  | Секунды | Среднее
Да | Microsoft.Logic/workflows | ActionThrottledEvents | Action Throttled Events | Количество | Итого
Да | Microsoft.Logic/workflows | BillableActionExecutions | Billable Action Executions | Количество | Итого
Да | Microsoft.Logic/workflows | BillableTriggerExecutions | Billable Trigger Executions | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageNativeOperation | Выставление счетов за внутренние операции | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageNativeOperation | Выставление счетов за внутренние операции | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageStandardConnector | Выставление счетов за операции стандартного соединителя | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageStandardConnector | Выставление счетов за операции стандартного соединителя | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Выставление счетов за операции с использованием хранилища | Количество | Итого
Да | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Выставление счетов за операции с использованием хранилища | Количество | Итого
Да | Microsoft.Logic/workflows | RunFailurePercentage | Run Failure Percentage | Процент | Итого
Да | Microsoft.Logic/workflows | RunLatency | Run Latency | Секунды | Среднее
Да | Microsoft.Logic/workflows | RunsCancelled | Выполнение отменено | Количество | Итого
Да | Microsoft.Logic/workflows | RunsCompleted | Runs Completed | Количество | Итого
Да | Microsoft.Logic/workflows | RunsFailed | Runs Failed | Количество | Итого
Да | Microsoft.Logic/workflows | RunsStarted | Runs Started | Количество | Итого
Да | Microsoft.Logic/workflows | RunsSucceeded | Runs Succeeded | Количество | Итого
Да | Microsoft.Logic/workflows | рунстартсроттледевентс | Запустить регулируемые события запуска | Количество | Итого
Да | Microsoft.Logic/workflows | RunSuccessLatency | Run Success Latency | Секунды | Среднее
Да | Microsoft.Logic/workflows | RunThrottledEvents | Run Throttled Events | Количество | Итого
Да | Microsoft.Logic/workflows | TotalBillableExecutions | Total Billable Executions | Количество | Итого
Да | Microsoft.Logic/workflows | TriggerFireLatency | Trigger Fire Latency  | Секунды | Среднее
Да | Microsoft.Logic/workflows | TriggerLatency | Trigger Latency  | Секунды | Среднее
Да | Microsoft.Logic/workflows | TriggersCompleted | Triggers Completed  | Количество | Итого
Да | Microsoft.Logic/workflows | TriggersFailed | Triggers Failed  | Количество | Итого
Да | Microsoft.Logic/workflows | TriggersFired | Triggers Fired  | Количество | Итого
Да | Microsoft.Logic/workflows | TriggersSkipped | Triggers Skipped | Количество | Итого
Да | Microsoft.Logic/workflows | TriggersStarted | Triggers Started  | Количество | Итого
Да | Microsoft.Logic/workflows | TriggersSucceeded | Triggers Succeeded  | Количество | Итого
Да | Microsoft.Logic/workflows | TriggerSuccessLatency | Trigger Success Latency  | Секунды | Среднее
Да | Microsoft.Logic/workflows | TriggerThrottledEvents | Trigger Throttled Events | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Активные ядра | Активные ядра | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Активные узлы | Активные узлы | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Завершенные запуски | Завершенные запуски | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | циклы выполнения со сбоем; | циклы выполнения со сбоем; | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Бездействующие ядра | Бездействующие ядра | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Бездействующие узлы | Бездействующие узлы | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Освобождение ядер | Освобождение ядер | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Покинуть узлы | Покинуть узлы | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Сбой развертывание модели | Сбой развертывание модели | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | развертывание модели запущено | развертывание модели запущено | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | развертывание модели успех | развертывание модели успех | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Не удалось зарегистрировать модель | Не удалось зарегистрировать модель | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Регистрация модели прошла | Регистрация модели прошла | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Вытесненные ядра | Вытесненные ядра | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Прерванные узлы | Прерванные узлы | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Процент использования квоты | Процент использования квоты | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Запущенные запуски | Запущенные запуски | Количество | Итого
Да | Microsoft.MachineLearningServices/workspaces | Всего ядер | Всего ядер | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Всего узлов | Всего узлов | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Непригодные для использования ядра | Непригодные для использования ядра | Количество | Среднее
Да | Microsoft.MachineLearningServices/workspaces | Неиспользуемые узлы | Неиспользуемые узлы | Количество | Среднее
Да | Microsoft.Maps/accounts | Доступность | Доступность | Процент | Среднее
Нет | Microsoft.Maps/accounts | Использование | Использование | Количество | Количество
Да | Microsoft. Media/mediaservices | ассеткаунт | Число ресурсов | Количество | Среднее
Да | Microsoft. Media/mediaservices | ассеткуота | Квота ресурса | Количество | Среднее
Да | Microsoft. Media/mediaservices | ассеткуотауседперцентаже | Процент используемой квоты актива | Процент | Среднее
Да | Microsoft. Media/mediaservices | контенткэйполицикаунт | Число политик ключей содержимого | Количество | Среднее
Да | Microsoft. Media/mediaservices | контенткэйполицикуота | Квота политики для ключа содержимого | Количество | Среднее
Да | Microsoft. Media/mediaservices | контенткэйполицикуотауседперцентаже | Процент использования квоты для ключа содержимого | Процент | Среднее
Да | Microsoft. Media/mediaservices | стреамингполицикаунт | Число политик потоковой передачи | Количество | Среднее
Да | Microsoft. Media/mediaservices | стреамингполицикуота | Квота политики потоковой передачи | Количество | Среднее
Да | Microsoft. Media/mediaservices | стреамингполицикуотауседперцентаже | Процент используемой квоты политики потоковой передачи | Процент | Среднее
Да | Microsoft. Media/mediaservices/Streamingendpoint | Исходящие | Исходящие | Байты | Итого
Да | Microsoft. Media/mediaservices/Streamingendpoint | Запросы | Запросы | Количество | Итого
Да | Microsoft. Media/mediaservices/Streamingendpoint | SuccessE2ELatency | Успешное завершение до окончания задержки | Миллисекунды | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | гкпаусетоталкаунт | Счетчик приостановки GC | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | гкпаусетоталтиме | Общее время приостановки сборки мусора | Миллисекунды | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | максолдженмеморипулбитес | Максимальный доступный размер данных старого поколения | Байты | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | олдженмеморипулбитес | Размер данных старого поколения | Байты | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | олдженпромотедбитес | Повысить до старого размера данных поколения | Байты | Максимальная
Да | Microsoft. Microservices4Spring/Аппклустерс | сервицекпуусажеперцентаже | Процент использования ЦП службой | Процент | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | сервицемеморикоммиттед | Назначенная память службы | Байты | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | сервицемеморимакс | Максимальная память службы | Байты | Максимальная
Да | Microsoft. Microservices4Spring/Аппклустерс | сервицемеморюсед | Используемая память службы | Байты | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | системкпуусажеперцентаже | Процент использования процессора системой | Процент | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатерроркаунт | Глобальная ошибка Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатрецеиведбитес | Всего получено байт в Tomcat | Байты | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатрекуестмакстиме | Максимальное время запроса Tomcat | Миллисекунды | Максимальная
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатрекуесттоталкаунт | Общее число запросов Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатрекуесттоталтиме | Общее время запроса Tomcat | Миллисекунды | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатреспонсеавгтиме | Среднее время запроса Tomcat | Миллисекунды | Среднее
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсентбитес | Всего отправленных байтов Tomcat | Байты | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионактивекурренткаунт | Счетчик активности сеансов Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионактивемакскаунт | Максимальное число активных сеансов Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионаливемакстиме | Максимальное время активности сеанса Tomcat | Миллисекунды | Максимальная
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионкреатедкаунт | Число созданных сеансов Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионекспиредкаунт | Счетчик с истекшим сроком действия сеанса Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | томкатсессионрежектедкаунт | Число отклоненных сеансов Tomcat | Количество | Итого
Да | Microsoft. Microservices4Spring/Аппклустерс | йоунгженпромотедбитес | Превращение в размер данных создания Титов. | Байты | Максимальная
Да | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Volume pool allocated used (Используемый размер выделенного пула для тома) | Байты | Среднее
Да | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Volume pool total logical size (Общий логический размер пула для тома) | Байты | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | AverageReadLatency | Средняя задержка чтения | MilliSeconds | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | AverageWriteLatency | Средняя задержка записи | MilliSeconds | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | ReadIops | Read iops (Число операций ввода-вывода в секунду при чтении) | Число/с | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | VolumeLogicalSize | Volume logical size (Логический размер тома) | Байты | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | VolumeSnapshotSize | Volume snapshot size (Размер моментальных снимков в томе) | Байты | Среднее
Да | Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes | WriteIops | Write iops (Количество операций ввода-вывода в секунду при записи) | Число/с | Среднее
Нет | Microsoft.Network/applicationGateways | аппликатионгатевайтоталтиме | Общее время шлюза приложений | MilliSeconds | Среднее
Нет | Microsoft.Network/applicationGateways | авгрекуесткаунтперхеалсихост | Количество запросов в минуту на исправный узел | Количество | Среднее
Нет | Microsoft.Network/applicationGateways | баккендконнекттиме | Время соединения серверной части | MilliSeconds | Среднее
Нет | Microsoft.Network/applicationGateways | баккендфирстбитереспонсетиме | Время ответа первого байта внутреннего сервера | MilliSeconds | Среднее
Нет | Microsoft.Network/applicationGateways | баккендластбитереспонсетиме | Время последнего ответа серверной части | MilliSeconds | Среднее
Да | Microsoft.Network/applicationGateways | баккендреспонсестатус | Состояние ответа серверной части | Количество | Итого
Да | Microsoft.Network/applicationGateways | блоккедкаунт | Распределение правил блокированных запросов брандмауэра веб-приложения | Количество | Итого
Да | Microsoft.Network/applicationGateways | блоккедреккаунт | Число заблокированных запросов брандмауэра веб-приложения | Количество | Итого
Да | Microsoft.Network/applicationGateways | BytesReceived | Получено байт | Байты | Итого
Да | Microsoft.Network/applicationGateways | BytesSent | Отправлено байт | Байты | Итого
Нет | Microsoft.Network/applicationGateways | капаЦитюнитс | Текущие единицы мощности | Количество | Среднее
Нет | Microsoft.Network/applicationGateways | клиентртт | RTT клиента | MilliSeconds | Среднее
Нет | Microsoft.Network/applicationGateways | компутеунитс | Текущие единицы вычислений | Количество | Среднее
Да | Microsoft.Network/applicationGateways | CurrentConnections | Текущие подключения. | Количество | Итого
Да | Microsoft.Network/applicationGateways | FailedRequests | Невыполненные запросы | Количество | Итого
Да | Microsoft.Network/applicationGateways | HealthyHostCount | Количество работоспособных узлов. | Количество | Среднее
Да | Microsoft.Network/applicationGateways | матчедкаунт | Общее распространение правила брандмауэра веб-приложения | Количество | Итого
Да | Microsoft.Network/applicationGateways | ResponseStatus | Состояние ответа. | Количество | Итого
Нет | Microsoft.Network/applicationGateways | Пропускная способность | Пропускная способность | Байт/с | Среднее
Да | Microsoft.Network/applicationGateways | тлспротокол | Клиентский протокол TLS | Количество | Итого
Да | Microsoft.Network/applicationGateways | TotalRequests | Общее количество запросов | Количество | Итого
Да | Microsoft.Network/applicationGateways | UnhealthyHostCount | Количество неработоспособных узлов | Количество | Среднее
Да | Microsoft.Network/azurefirewalls | аппликатионрулехит | Число попаданий правил приложения | Количество | Итого
Да | Microsoft.Network/azurefirewalls | Обработка обработанных | Обработанные данные | Байты | Итого
Да | Microsoft.Network/azurefirewalls | фиреваллхеалс | Состояние работоспособности брандмауэра | Процент | Среднее
Да | Microsoft.Network/azurefirewalls | нетворкрулехит | Число попаданий сетевых правил | Количество | Итого
Да | Microsoft.Network/azurefirewalls | снатпортутилизатион | Использование портов SNAT | Процент | Среднее
Да | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | Число/с | Среднее
Да | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | Число/с | Среднее
Да | Microsoft.Network/dnszones | QueryVolume | Объем запросов | Количество | Итого
Нет | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Использование емкости, доступной для наборов записей | Процент | Максимальная
Да | Microsoft.Network/dnszones | RecordSetCount | Количество наборов записей | Количество | Максимальная
Да | Microsoft.Network/expressRouteCircuits | арпаваилабилити | Доступность ARP | Процент | Среднее
Да | Microsoft.Network/expressRouteCircuits | бгпаваилабилити | Доступность BGP | Процент | Среднее
Нет | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | Число/с | Среднее
Нет | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | Число/с | Среднее
Нет | Microsoft.Network/expressRouteCircuits | глобалреачбитсинперсеконд | глобалреачбитсинперсеконд | Число/с | Среднее
Нет | Microsoft.Network/expressRouteCircuits | глобалреачбитсаутперсеконд | глобалреачбитсаутперсеконд | Число/с | Среднее
Нет | Microsoft.Network/expressRouteCircuits | косдропбитсинперсеконд | дроппединбитсперсеконд | Число/с | Среднее
Нет | Microsoft.Network/expressRouteCircuits | косдропбитсаутперсеконд | дроппедаутбитсперсеконд | Число/с | Среднее
Да | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | Число/с | Среднее
Да | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | Число/с | Среднее
Нет | Microsoft. Network/Експрессраутегатевайс | ергатевайконнектионбитсинперсеконд | BitsInPerSecond | Число/с | Среднее
Нет | Microsoft. Network/Експрессраутегатевайс | ергатевайконнектионбитсаутперсеконд | BitsOutPerSecond | Число/с | Среднее
Да | Microsoft. Network/Експрессраутепортс | админстате | админстате | Количество | Среднее
Да | Microsoft. Network/Експрессраутепортс | линепротокол | линепротокол | Количество | Среднее
Да | Microsoft. Network/Експрессраутепортс | портбитсинперсеконд | BitsInPerSecond | Число/с | Среднее
Да | Microsoft. Network/Експрессраутепортс | портбитсаутперсеконд | BitsOutPerSecond | Число/с | Среднее
Да | Microsoft. Network/Експрессраутепортс | ркслигхтлевел | ркслигхтлевел | Количество | Среднее
Да | Microsoft. Network/Експрессраутепортс | ткслигхтлевел | ткслигхтлевел | Количество | Среднее
Да | Microsoft.Network/frontdoors | BackendHealthPercentage | Работоспособность серверного компонента (в процентах) | Процент | Среднее
Да | Microsoft.Network/frontdoors | BackendRequestCount | Число запросов к серверному компоненту | Количество | Итого
Да | Microsoft.Network/frontdoors | BackendRequestLatency | Backend Request Latency (Задержка запросов к серверным частям) | MilliSeconds | Среднее
Да | Microsoft.Network/frontdoors | биллаблереспонсесизе | Оплачиваемый размер ответа | Байты | Итого
Да | Microsoft.Network/frontdoors | RequestCount | Число запросов | Количество | Итого
Да | Microsoft.Network/frontdoors | RequestSize | Размер запроса | Байты | Итого
Да | Microsoft.Network/frontdoors | ResponseSize | Размер ответа | Байты | Итого
Да | Microsoft.Network/frontdoors | TotalLatency | Total Latency (Общая задержка) | MilliSeconds | Среднее
Да | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения) | Количество | Итого
Нет | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Allocated SNAT Ports (Preview) (Выделенные порты SNAT (предварительная версия)) | Количество | Итого
Да | Microsoft.Network/loadBalancers | ByteCount | Количество байтов | Количество | Итого
Да | Microsoft.Network/loadBalancers | DipAvailability | Health Probe Status (Состояние пробы работоспособности) | Количество | Среднее
Да | Microsoft.Network/loadBalancers | PacketCount | Количество пакетов | Количество | Итого
Да | Microsoft.Network/loadBalancers | SnatConnectionCount | Количество подключений SNAT | Количество | Итого
Да | Microsoft.Network/loadBalancers | SYNCount | Количество пакетов SYN | Количество | Итого
Нет | Microsoft.Network/loadBalancers | UsedSnatPorts | Used SNAT Ports (Preview) (Используемые порты SNAT (предварительная версия)) | Количество | Итого
Да | Microsoft.Network/loadBalancers | VipAvailability | Data Path Availability (Доступность пути к данным) | Количество | Среднее
Да | Microsoft.Network/networkInterfaces | BytesReceivedRate | Получено байт | Байты | Итого
Да | Microsoft.Network/networkInterfaces | BytesSentRate | Отправлено байт | Байты | Итого
Да | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Получено пакетов | Количество | Итого
Да | Microsoft.Network/networkInterfaces | PacketsSentRate | Отправлено пакетов | Количество | Итого
Да | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Среднее время приема-передачи (МС) | MilliSeconds | Среднее
Да | Microsoft.Network/networkWatchers/connectionMonitors | чекксфаиледперцент | Процент неудачных проверок (Предварительная версия) | Процент | Среднее
Да | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | Доля проб с ошибками, в процентах | Процент | Среднее
Да | Microsoft.Network/networkWatchers/connectionMonitors | раундтриптимемс | Время приема-передачи (МС) (Предварительная версия) | MilliSeconds | Среднее
Да | Microsoft.Network/publicIPAddresses | ByteCount | Количество байтов | Количество | Итого
Да | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Удаленные входящие байты (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Перенаправленные входящие байты (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | BytesInDDoS | Входящие байты (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Входящие пакеты SYN для активации защиты от атаки DDoS | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Входящие пакеты TCP для активации защиты от атаки DDoS | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Входящие пакеты UDP для активации защиты от атаки DDoS | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | Выполняется ли атака DDoS | Количество | Максимальная
Да | Microsoft.Network/publicIPAddresses | PacketCount | Количество пакетов | Количество | Итого
Да | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Удаленные входящие пакеты (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Перенаправленные входящие пакеты (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Входящие пакеты (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | SynCount | Количество пакетов SYN | Количество | Итого
Да | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | Удаленные входящие байты TCP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | Перенаправленные входящие байты TCP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Входящие байты TCP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | Удаленные входящие пакеты TCP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | Перенаправленные входящие пакеты TCP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | Входящие пакеты TCP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | Удаленные входящие байты UDP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | Перенаправленные входящие байты UDP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | Входящие байты UDP (атака DDoS) | Байт/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | Удаленные входящие пакеты UDP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | Перенаправленные входящие пакеты UDP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | Входящие пакеты UDP (атака DDoS) | Число/с | Максимальная
Да | Microsoft.Network/publicIPAddresses | VipAvailability | Data Path Availability (Доступность пути к данным) | Количество | Среднее
Да | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Проверка состояния конечной точки с помощью конечной точки | Количество | Максимальная
Да | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Запросы, выполняемые возвращаемой конечной точкой | Количество | Итого
Да | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Gateway S2S Bandwidth (Пропускная способность шлюза S2S) | Байт/с | Среднее
Да | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Gateway P2S Bandwidth (Пропускная способность шлюза P2S) | Байт/с | Среднее
Да | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | P2S Connection Count (Количество подключений P2S) | Количество | Максимальная
Да | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Пропускная способность туннеля | Байт/с | Среднее
Да | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Исходящие байты туннеля | Байты | Итого
Да | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Удаленные входящие пакеты туннеля (несоответствие селектора трафика) | Количество | Итого
Да | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Исходящие пакеты туннеля | Количество | Итого
Да | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Входящие байты туннеля | Байты | Итого
Да | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Удаленные исходящие пакеты туннеля (несоответствие селектора трафика) | Количество | Итого
Да | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Входящие пакеты туннеля | Количество | Итого
Да | Microsoft.Network/virtualNetworks | пингмешаверажераундтрипмс | Время кругового пути для проверки связи с виртуальной машиной | MilliSeconds | Среднее
Да | Microsoft.Network/virtualNetworks | пингмешпробесфаиледперцент | Неудачные проверки связи с виртуальной машиной | Процент | Среднее
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming | Входящие сообщения | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Incoming.all.failedrequests | Все неудачные входящие запросы | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | Все входящие запросы | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | Отправлено запланированных push-уведомлений | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | Отменено запланированных push-уведомлений | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all | Операции управления установкой | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Операции удаления установки | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | Операции получения установки | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | Операции исправления установки | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | Операции создания или обновления установки | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | Все исходящие уведомления | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Ошибки из-за поврежденного или просроченного канала | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | Ошибки канала | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | Ошибки полезных данных | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | Ошибки внешней системы уведомлений | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | Успешные уведомления | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Ошибка из-за поврежденного канала APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Ошибка из-за просроченного канала APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | Ошибки авторизации APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | Ошибка из-за недопустимого размера уведомления APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | Ошибки APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Успешные уведомления APNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | Ошибки проверки подлинности GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | Ошибка из-за поврежденного канала GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | Ошибка из-за просроченного канала GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | Ошибки авторизации GCM (недопустимые учетные данные) | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | Недопустимый формат уведомления GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | Ошибка из-за недопустимого размера уведомления GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | Ошибки GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | Успешные уведомления GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | Регулируемые уведомления GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | Ошибка из-за неправильного канала GCM | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | Ошибки проверки подлинности MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | Ошибка из-за поврежденного канала MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | Канал MPNS отсоединен | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | Пропущенные уведомления MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | Недопустимые учетные данные MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | Недопустимый формат уведомления MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | Ошибки MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | Успешные уведомления MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | Регулируемые уведомления MPNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | Ошибки проверки подлинности WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | Ошибка из-за поврежденного канала WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | Канал WNS отсоединен | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | Канал WNS регулируется | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | Пропущенные уведомления WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | Ошибка из-за просроченного канала WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | Ошибки авторизации WNS (недопустимые учетные данные) | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | Недопустимый формат уведомления WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | Ошибка из-за недопустимого размера уведомления WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | Ошибки авторизации WNS (недопустимый маркер) | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | Ошибки WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | Успешные уведомления WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | Регулируемые уведомления WNS | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | Ошибки авторизации WNS (нет доступа) | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | Ошибки авторизации WNS (неправильный маркер) | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | Операции регистрации | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | Операции создания регистрации | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | Операции удаления регистрации | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | Операции чтения регистрации | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | Операции обновления регистрации | Количество | Итого
Да | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | Запланированных уведомлений в состоянии ожидания | Количество | Итого
Да | Microsoft.OperationalInsights/workspaces | Average_% Available Memory | Процент доступной памяти | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Available Swap Space | Процент доступной области подкачки | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Committed Bytes In Use | Использование выделенной памяти (в байтах), % | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% DPC Time | Процент времени DPC | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Free Inodes | Процент свободных индексных дескрипторов | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Free Space | Процент свободного места | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Free Space | Процент свободного места | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Idle Time | Процент времени простоя | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Interrupt Time | Процент времени прерывания | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% IO Wait Time | Процент времени ожидания ввода-вывода | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Nice Time | Процент времени работы с низким приоритетом | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Privileged Time | Процент времени работы в привилегированном режиме | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % загруженности процессора | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % загруженности процессора | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Used Inodes | Процент используемых индексных дескрипторов | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Used Memory | Процент используемой памяти | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Used Space | Процент используемого места | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% Used Swap Space | Процент используемой области подкачки | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_% User Time | Процент времени пользователя | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Available MBytes | Доступный объем в МБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Memory | Доступный объем памяти в МБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Swap | Доступный объем подкачки в МБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Avg. время чтения с диска (с) | Среднее время чтения с диска (с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Avg. время чтения с диска (с) | Среднее время чтения с диска (с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Avg. время обращения к диску (с) | Среднее время обращения к диску (сек.) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Avg. время записи на диск (с) | Среднее время записи на диск (сек.) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Avg. время записи на диск (с) | Среднее время записи на диск (сек.) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Bytes Received/sec | Полученных байтов/с | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Bytes Sent/sec | Отправленных байтов/с | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Bytes Total/sec | Всего байтов/с | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Current Disk Queue Length | Текущая длина очереди диска | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Read Bytes/sec | Скорость чтения с диска (байт/с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Операций чтения с диска в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Операций чтения с диска в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Обращений к диску в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Обращений к диску в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Write Bytes/sec | Скорость записи на диск (байт/с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Операций записи на диск в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Операций записи на диск в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Свободно мегабайт | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Свободно мегабайт | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Free Physical Memory | Объем свободной физической памяти | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Free Space in Paging Files | Объем свободного места в файлах подкачки | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Free Virtual Memory | Объем свободной виртуальной памяти | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Logical Disk Bytes/sec | Скорость обмена с логическим диском (байт/с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Page Reads/sec | Операций чтения со страницы в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Page Writes/sec | Операций записи на страницу в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | Страниц в секунду | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Pct Privileged Time | Процент времени работы в привилегированном режиме | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Pct User Time | Процент времени пользователя | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Physical Disk Bytes/sec | Скорость обмена с физическим диском (байт/с) | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Processes | Процессы | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Processor Queue Length | Длина очереди процессора | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Size Stored In Paging Files | Объем, сохраненный в файлах подкачки | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Bytes | Всего байт | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Received | Всего получено байт | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Transmitted | Всего передано байт | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Collisions | Всего конфликтов | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Packets Received | Всего получено пакетов | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Packets Transmitted | Всего передано пакетов | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Rx Errors | Всего ошибок Rx | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Total Tx Errors | Всего ошибок Tx | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Uptime | Время доступности | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Used MBytes Swap Space | Используемая области подкачки в МБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Used Memory kBytes | Используемая память в КБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Used Memory MBytes | Используемый объем памяти в МБ | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Users | Пользователи | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Average_Virtual Shared Memory | Виртуальная общая память | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Мероприятие | Мероприятие | Количество | Среднее
Да | Microsoft.OperationalInsights/workspaces | Пульс | Пульс | Количество | Итого
Да | Microsoft.OperationalInsights/workspaces | Обновить | Обновить | Количество | Среднее
Да | Microsoft.PowerBIDedicated/capacities | memory_metric | Память | Байты | Среднее
Да | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Пробуксовка памяти (наборы данных) | Процент | Среднее
Да | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | Высокая загрузка QPU | Количество | Итого
Да | Microsoft.PowerBIDedicated/capacities | QueryDuration | Длительность запроса (наборы данных) | Миллисекунды | Среднее
Да | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Длина очереди заданий пула запросов (наборы данных) | Количество | Среднее
Нет | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | Количество | Итого
Нет | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | Количество | Итого
Да | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | Количество | Итого
Нет | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | Количество | Итого
Нет | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | Количество | Итого
Нет | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | Количество | Итого
Нет | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Количество | Итого
Нет | Microsoft.Relay/namespaces | ListenerDisconnects | ListenerDisconnects | Количество | Итого
Нет | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | Количество | Итого
Нет | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | Количество | Итого
Нет | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | Количество | Итого
Нет | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Количество | Итого
Нет | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | Количество | Итого
Да | Microsoft.Search/searchServices | SearchLatency | Search Latency | Секунды | Среднее
Да | Microsoft.Search/searchServices | SearchQueriesPerSecond | Search queries per second | Число/с | Среднее
Да | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Throttled search queries percentage | Процент | Среднее
Нет | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | ActiveMessages | Число активных сообщений в очереди или разделе. | Количество | Среднее
Нет | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Закрытые подключения. | Количество | Среднее
Нет | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Открытые подключения. | Количество | Среднее
Нет | Microsoft.ServiceBus/namespaces | CPUXNS | ЦП (не рекомендуется) | Процент | Максимальная
Нет | Microsoft.ServiceBus/namespaces | деадлеттередмессажес | Количество недоставленных сообщений в очереди или разделе. | Количество | Среднее
Да | Microsoft.ServiceBus/namespaces | IncomingMessages | Входящие сообщения | Количество | Итого
Да | Microsoft.ServiceBus/namespaces | IncomingRequests | Входящих запросов | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | Сообщения | Число сообщений в очереди или разделе. | Количество | Среднее
Нет | Microsoft.ServiceBus/namespaces | намеспацекпуусаже | ЦП | Процент | Максимальная
Нет | Microsoft.ServiceBus/namespaces | намеспацемеморюсаже | Использование памяти | Процент | Максимальная
Да | Microsoft.ServiceBus/namespaces | OutgoingMessages | Исходящие сообщения | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | счедуледмессажес | Число запланированных сообщений в очереди или разделе. | Количество | Среднее
Нет | Microsoft.ServiceBus/namespaces | ServerErrors | Ошибки сервера. | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | Размер | Размер | Байты | Среднее
Нет | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Выполненные запросы | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | ThrottledRequests | Регулируемые запросы. | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | UserErrors | Ошибки пользователей. | Количество | Итого
Нет | Microsoft.ServiceBus/namespaces | WSXNS | Использование памяти (не рекомендуется) | Процент | Максимальная
Нет | Microsoft. Сервицефабрикмеш/приложения | актуалкпу | актуалкпу | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | актуалмемори | актуалмемори | Байты | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | аллокатедкпу | аллокатедкпу | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | аллокатедмемори | аллокатедмемори | Байты | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | аппликатионстатус | аппликатионстатус | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | контаинерстатус | контаинерстатус | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | График | График | Процент | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | меморютилизатион | меморютилизатион | Процент | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | рестарткаунт | рестарткаунт | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | сервицерепликастатус | сервицерепликастатус | Количество | Среднее
Нет | Microsoft. Сервицефабрикмеш/приложения | сервицестатус | сервицестатус | Количество | Среднее
Да | Microsoft.SignalRService/SignalR | ConnectionCount | Число подключений | Количество | Максимальная
Да | Microsoft.SignalRService/SignalR | InboundTraffic | Inbound Traffic (Входящий трафик) | Байты | Итого
Да | Microsoft.SignalRService/SignalR | MessageCount | Количество сообщений | Количество | Итого
Да | Microsoft.SignalRService/SignalR | OutboundTraffic | Outbound Traffic (Исходящий трафик) | Байты | Итого
Да | Microsoft.SignalRService/SignalR | SystemErrors | Системные ошибки | Процент | Максимальная
Да | Microsoft.SignalRService/SignalR | UserErrors | Ошибки пользователей | Процент | Максимальная
Да | Microsoft.Sql/managedInstances | avg_cpu_percent | Average CPU percentage (Средний процент использования ЦП) | Процент | Среднее
Да | Microsoft.Sql/managedInstances | io_bytes_read | Количество считанных байт ввода-вывода | Байты | Среднее
Да | Microsoft.Sql/managedInstances | io_bytes_written | Количество записанных байт ввода-вывода | Байты | Среднее
Да | Microsoft.Sql/managedInstances | io_requests | IO requests count (Количество запросов ввода-вывода) | Количество | Среднее
Да | Microsoft.Sql/managedInstances | reserved_storage_mb | Зарезервированное дисковое пространство | Количество | Среднее
Да | Microsoft.Sql/managedInstances | storage_space_used_mb | Использованное дисковое пространство | Количество | Среднее
Да | Microsoft.Sql/managedInstances | virtual_core_count | Virtual core count (Число виртуальных ядер) | Количество | Среднее
Нет | Microsoft.Sql/servers | database_dtu_consumption_percent | Процент использования DTU | Процент | Среднее
Нет | Microsoft.Sql/servers | database_storage_used | Используемое пространство данных | Байты | Среднее
Да | Microsoft.Sql/servers | dtu_consumption_percent | Процент использования DTU | Процент | Среднее
Да | Microsoft.Sql/servers | dtu_used | DTU used | Количество | Среднее
Да | Microsoft.Sql/servers | storage_used | Используемое пространство данных | Байты | Среднее
Да | Microsoft.Sql/servers/databases | allocated_data_storage | Выделенное пространство данных | Байты | Среднее
Да | Microsoft.Sql/servers/databases | app_cpu_billed | Выплата за ЦП приложения | Количество | Итого
Да | Microsoft.Sql/servers/databases | app_cpu_percent | Процент загрузки ЦП приложения | Процент | Среднее
Да | Microsoft.Sql/servers/databases | app_memory_percent | Процент памяти приложения | Процент | Среднее
Да | Microsoft.Sql/servers/databases | blocked_by_firewall | Заблокировано брандмауэром | Количество | Итого
Да | Microsoft.Sql/servers/databases | cache_hit_percent | Cache hit percentage (Процент попаданий в кэш) | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | cache_used_percent | Cache used percentage (Процент использования кэша) | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | connection_failed | Неудачные подключения | Количество | Итого
Да | Microsoft.Sql/servers/databases | connection_successful | Успешные подключения | Количество | Итого
Да | Microsoft.Sql/servers/databases | cpu_limit | Ограничение ЦП | Количество | Среднее
Да | Microsoft.Sql/servers/databases | cpu_percent | Процент использования ЦП | Процент | Среднее
Да | Microsoft.Sql/servers/databases | cpu_used | Используемый ЦП | Количество | Среднее
Да | Microsoft.Sql/servers/databases | взаимоблокировка | Взаимоблокировки | Количество | Итого
Да | Microsoft.Sql/servers/databases | dtu_consumption_percent | Процент использования DTU | Процент | Среднее
Да | Microsoft.Sql/servers/databases | dtu_limit | Лимит DTU | Количество | Среднее
Да | Microsoft.Sql/servers/databases | dtu_used | DTU used | Количество | Среднее
Да | Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU percentage | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | dwu_limit | Лимит DWU | Количество | Максимальная
Да | Microsoft.Sql/servers/databases | dwu_used | DWU used | Количество | Максимальная
Да | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Local tempdb percentage (Процент использования локальной базы данных tempdb) | Процент | Среднее
Да | Microsoft.Sql/servers/databases | log_write_percent | Log IO percentage | Процент | Среднее
Да | Microsoft.Sql/servers/databases | memory_usage_percent | Процент памяти | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | physical_data_read_percent | Процент операций ввода/вывода данных | Процент | Среднее
Да | Microsoft.Sql/servers/databases | sessions_percent | Процент использования сеансов | Процент | Среднее
Да | Microsoft.Sql/servers/databases | хранилище | Используемое пространство данных | Байты | Максимальная
Да | Microsoft.Sql/servers/databases | storage_percent | Процент использования пространства данных | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | tempdb_data_size | Размер файла данных tempdb, КБ | Количество | Максимальная
Да | Microsoft.Sql/servers/databases | tempdb_log_size | Размер файла журнала tempdb, КБ | Количество | Максимальная
Да | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Используемый журнал в процентах tempdb | Процент | Максимальная
Да | Microsoft.Sql/servers/databases | workers_percent | Workers percentage | Процент | Среднее
Да | Microsoft.Sql/servers/databases | xtp_storage_percent | Процент хранилища выполняющейся в памяти OLTP | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Выделенное пространство данных | Байты | Среднее
Да | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Процент выделенного пространства данных | Процент | Максимальная
Да | Microsoft.Sql/servers/elasticPools | cpu_limit | Ограничение ЦП | Количество | Среднее
Да | Microsoft.Sql/servers/elasticPools | cpu_percent | Процент использования ЦП | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | cpu_used | Используемый ЦП | Количество | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Выделенное пространство данных | Байты | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_cpu_limit | Ограничение ЦП | Количество | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Процент использования ЦП | Процент | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_cpu_used | Используемый ЦП | Количество | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | Процент использования DTU | Процент | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_eDTU_used | eDTU used | Количество | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Log IO percentage | Процент | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Процент операций ввода/вывода данных | Процент | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Процент использования сеансов | Процент | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_storage_used | Используемое пространство данных | Байты | Среднее
Нет | Microsoft.Sql/servers/elasticPools | database_workers_percent | Workers percentage | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Процент использования DTU | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU limit | Количество | Среднее
Да | Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU used | Количество | Среднее
Да | Microsoft.Sql/servers/elasticPools | log_write_percent | Log IO percentage | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Процент операций ввода/вывода данных | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | sessions_percent | Процент использования сеансов | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | storage_limit | Максимальный размер данных | Байты | Среднее
Да | Microsoft.Sql/servers/elasticPools | storage_percent | Процент использования пространства данных | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | storage_used | Используемое пространство данных | Байты | Среднее
Да | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Размер файла данных tempdb, КБ | Количество | Максимальная
Да | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Размер файла журнала tempdb, КБ | Количество | Максимальная
Да | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Используемый журнал в процентах tempdb | Процент | Максимальная
Да | Microsoft.Sql/servers/elasticPools | workers_percent | Workers percentage | Процент | Среднее
Да | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Процент хранилища выполняющейся в памяти OLTP | Процент | Среднее
Да | Microsoft.Storage/storageAccounts | Доступность | Доступность | Процент | Среднее
Да | Microsoft.Storage/storageAccounts | Исходящие | Исходящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts | Входящие | Входящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts | Транзакции | Транзакции | Количество | Итого
Нет | Microsoft.Storage/storageAccounts | UsedCapacity | Используемая емкость | Байты | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | Доступность | Доступность | Процент | Среднее
Нет | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Емкость больших двоичных объектов | Байты | Среднее
Нет | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Количество больших двоичных объектов | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Количество контейнеров больших двоичных объектов | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | Исходящие | Исходящие | Байты | Итого
Нет | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Емкость индекса | Байты | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | Входящие | Входящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/blobServices | Транзакции | Транзакции | Количество | Итого
Да | Microsoft.Storage/storageAccounts/fileServices | Доступность | Доступность | Процент | Среднее
Да | Microsoft.Storage/storageAccounts/fileServices | Исходящие | Исходящие | Байты | Итого
Нет | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | Емкость файла | Байты | Среднее
Нет | Microsoft.Storage/storageAccounts/fileServices | FileCount | Количество файлов | Количество | Среднее
Нет | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Количество общих файловых ресурсов | Количество | Среднее
Нет | Microsoft.Storage/storageAccounts/fileServices | филешарекуота | Размер квоты общей папки | Байты | Среднее
Нет | Microsoft.Storage/storageAccounts/fileServices | филешареснапшоткаунт | Число моментальных снимков общей папки | Количество | Среднее
Нет | Microsoft.Storage/storageAccounts/fileServices | филешареснапшотсизе | Размер моментального снимка файлового ресурса | Байты | Среднее
Да | Microsoft.Storage/storageAccounts/fileServices | Входящие | Входящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/fileServices | Транзакции | Транзакции | Количество | Итого
Да | Microsoft.Storage/storageAccounts/queueServices | Доступность | Доступность | Процент | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | Исходящие | Исходящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/queueServices | Входящие | Входящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Емкость очереди | Байты | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Количество очередей | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Количество сообщений очереди | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/queueServices | Транзакции | Транзакции | Количество | Итого
Да | Microsoft.Storage/storageAccounts/tableServices | Доступность | Доступность | Процент | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | Исходящие | Исходящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/tableServices | Входящие | Входящие | Байты | Итого
Да | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Успешно выполненная задержка E2E | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Успешно выполненная задержка сервера | Миллисекунды | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Емкость таблицы | Байты | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | TableCount | Количество таблиц | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Количество сущностей таблиц | Количество | Среднее
Да | Microsoft.Storage/storageAccounts/tableServices | Транзакции | Транзакции | Количество | Итого
Да | Microsoft. Сторажекаче/кэши | клиентиопс | Всего операций ввода-вывода клиента | Количество | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентлатенци | Средняя задержка клиента | Миллисекунды | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентлоккиопс | Операции ввода-вывода блокировки клиента | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентметадатареадиопс | ОПЕРАЦИЙ чтения метаданных клиента | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентметадатавритеиопс | Запись метаданных клиента в операции ввода-вывода | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентреадиопс | Операции чтения клиента в секунду | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентреадсраугхпут | Средняя пропускная способность чтения кэша | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентвритеиопс | Число операций записи клиента в секунду | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | клиентвритесраугхпут | Средняя пропускная способность записи в кэш | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетасинквритесраугхпут | Пропускная способность асинхронной записи Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетфиллсраугхпут | Пропускная способность заполнения Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржесеалс | Работоспособность целевого объекта хранилища | Количество | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетиопс | Всего Сторажетаржет операций ввода-вывода | Количество | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетлатенци | Задержка Сторажетаржет | Миллисекунды | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетметадатареадиопс | Сторажетаржет чтение метаданных операций ввода-вывода | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетметадатавритеиопс | Сторажетаржет запись метаданных в секунду | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетреадахеадсраугхпут | Пропускная способность чтения Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетреадиопс | Сторажетаржет чтение операций ввода-вывода | Число/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетсинквритесраугхпут | Пропускная способность синхронной записи Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржеттоталреадсраугхпут | Общая пропускная способность чтения Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржеттоталвритесраугхпут | Общая пропускная способность записи Сторажетаржет | Байт/с | Среднее
Да | Microsoft. Сторажекаче/кэши | сторажетаржетвритеиопс | Сторажетаржет запись в секунду | Количество | Среднее
Да | Microsoft. Сторажекаче/кэши | Время доступности | Время доступности | Количество | Среднее
Да | Microsoft. storagesync/Сторажесинксервицес | серверсинксессионресулт | Результат сеанса синхронизации | Количество | Среднее
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинкбатчтрансферредфилебитес | Синхронизировано байт | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинкрекалледнетворкбитесбяппликатион | Размер отзыва по уровням облака по приложениям | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинкрекалледтоталнетворкбитес | Размер отзыва при уровне облака | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинкрекаллиототалсизебитес | Отзыв уровней в облаке | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинкрекаллсраугхпутбитесперсеконд | Пропускная способность отзыва облачного уровня | Байт/с | Среднее
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинксерверхеартбеат | Состояние сервера в сети | Количество | Максимальная
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинксинксессионапплиедфилескаунт | Синхронизировано файлов | Количество | Итого
Да | Microsoft. storagesync/Сторажесинксервицес | сторажесинксинксессионперитемеррорскаунт | Несинхронизирующиеся файлы | Количество | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс | серверхеартбеат | Состояние сервера в сети | Количество | Максимальная
Да | Microsoft. storagesync/Сторажесинксервицес/Регистередсерверс | серверрекаллиототалсизебитес | Отзыв уровней в облаке | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс | синкграупбатчтрансферредфилебитес | Синхронизировано байт | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс | синкграупсинксессионапплиедфилескаунт | Синхронизировано файлов | Количество | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс | синкграупсинксессионперитемеррорскаунт | Несинхронизирующиеся файлы | Количество | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс | серверендпоинтбатчтрансферредфилебитес | Синхронизировано байт | Байты | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс | серверендпоинтсинксессионапплиедфилескаунт | Синхронизировано файлов | Количество | Итого
Да | Microsoft. storagesync/Сторажесинксервицес/Синкграупс/Серверендпоинтс | серверендпоинтсинксессионперитемеррорскаунт | Несинхронизирующиеся файлы | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Неудачные запросы функций | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | События функций | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Запросы функций | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Ошибки преобразования данных | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Ошибки десериализации входа | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Неупорядоченные события | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Ранние входные события | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | ошибки | Ошибки среды выполнения | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Байты входного события | Байты | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Входные события | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | инпутевентссаурцесбакклогжед | Необработанные входные события | Количество | Максимальная
Да | Microsoft.StreamAnalytics/streamingjobs | инпутевентссаурцесперсеконд | Полученные входные источники | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | События позднего ввода | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Выходные события | Количество | Итого
Да | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Предельная задержка | Секунды | Максимальная
Да | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | Использование единиц потоковой передачи (%) | Процент | Максимальная
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Скорость чтения с диска | Скорость чтения с диска | Байты | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | Число/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Disk Write Bytes | Disk Write Bytes | Байты | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | Число/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дискреадбитесперсеконд | Disk Read Bytes/Sec | Байт/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дискреадлатенци | Задержка чтения с диска | Миллисекунды | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дискреадоператионс | Операции чтения с диска | Количество | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дисквритебитесперсеконд | Disk Write Bytes/Sec | Байт/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дискврителатенци | Задержка записи на диск | Миллисекунды | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | дисквритеоператионс | Операции записи на диск | Количество | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | меморяктиве | Память активна | Байты | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | меморигрантед | Предоставленная память | Байты | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | MemoryUsed | Используемая память | Байты | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Сеть (входящий трафик) | Сеть (входящий трафик) | Байты | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Сеть (исходящий трафик) | Сеть (исходящий трафик) | Байты | Итого
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | нетворкинбитесперсеконд | Сеть в байтах/с | Байт/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | нетворкаутбитесперсеконд | Сетевых исходящих байт/с | Байт/с | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | Percentage CPU | Percentage CPU | Процент | Среднее
Да | Microsoft. Вмвареклаудсимпле/virtualMachines | перцентажекпуреади | Процент готовности ЦП | Миллисекунды | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Активные запросы | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Среднее время ответа: | Секунды | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Входящие данные: | Байты | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Выходные данные | Байты | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Процент использования ЦП | Процент | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Длина дисковой очереди | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | HTTP 2xx | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | HTTP 3xx: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | HTTP 403: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | HTTP 404: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | HTTP 406: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | HTTP 4xx: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Ошибки HTTP-сервера: | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Длина очереди HTTP: | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Рабочие процессы плана службы приложений уровня "Крупный" | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Рабочие процессы плана службы приложений уровня "Средний" | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Процент использования памяти | Процент | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | Запросы | Запросы | Количество | Итого
Да | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Рабочие роли плана службы приложений уровня "Малый" | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Общее число внешних интерфейсов | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | Процент использования ЦП | Процент | Среднее
Да | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | Процент использования памяти | Процент | Среднее
Да | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | Доступные рабочие процессы | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | Общее количество рабочих процессов | Количество | Среднее
Да | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | Использованные рабочие процессы | Количество | Среднее
Да | Microsoft.Web/serverfarms | BytesReceived | Входящие данные: | Байты | Итого
Да | Microsoft.Web/serverfarms | BytesSent | Выходные данные | Байты | Итого
Да | Microsoft.Web/serverfarms | CpuPercentage | Процент использования ЦП | Процент | Среднее
Да | Microsoft.Web/serverfarms | DiskQueueLength | Длина дисковой очереди | Количество | Среднее
Да | Microsoft.Web/serverfarms | HttpQueueLength | Длина очереди HTTP: | Количество | Среднее
Да | Microsoft.Web/serverfarms | MemoryPercentage | Процент использования памяти | Процент | Среднее
Да | Microsoft.Web/serverfarms | ткпклосеваит | Ожидание TCP-закрытия | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткпклосинг | TCP-закрытие | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткпестаблишед | Установлен TCP | Количество | Среднее
Да | Microsoft.Web/serverfarms | TcpFinWait1 | Ожидание TCP FIN 1 | Количество | Среднее
Да | Microsoft.Web/serverfarms | TcpFinWait2 | Ожидание TCP FIN 2 | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткпластакк | Последнее подтверждение TCP | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткпсинрецеивед | Получен TCP SYN | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткпсинсент | TCP SYN отправлен | Количество | Среднее
Да | Microsoft.Web/serverfarms | ткптимеваит | Время ожидания TCP | Количество | Среднее
Да | Microsoft.Web/sites | AppConnections | Соединения | Количество | Среднее
Да | Microsoft.Web/sites | AverageMemoryWorkingSet | средний размер рабочего набора памяти; | Байты | Среднее
Да | Microsoft.Web/sites | AverageResponseTime | Среднее время ответа: | Секунды | Среднее
Да | Microsoft.Web/sites | BytesReceived | Входящие данные: | Байты | Итого
Да | Microsoft.Web/sites | BytesSent | Выходные данные | Байты | Итого
Да | Microsoft.Web/sites | CpuTime | Время ЦП: | Секунды | Итого
Да | Microsoft.Web/sites | CurrentAssemblies | Текущее число сборок | Количество | Среднее
Да | Microsoft.Web/sites | FunctionExecutionCount | Function Execution Count | Количество | Итого
Да | Microsoft.Web/sites | FunctionExecutionUnits | Function Execution Units | Количество | Итого
Да | Microsoft.Web/sites | Gen0Collections | Сборок мусора поколения 0 | Количество | Итого
Да | Microsoft.Web/sites | Gen1Collections | Сборок мусора поколения 1 | Количество | Итого
Да | Microsoft.Web/sites | Gen2Collections | Сборок мусора поколения 2 | Количество | Итого
Да | Microsoft.Web/sites | Маркеры | Счетчик дескрипторов | Количество | Среднее
Да | Microsoft.Web/sites | хеалсчеккстатус | Состояние проверки работоспособности | Количество | Среднее
Да | Microsoft.Web/sites | Http101 | Http 101 | Количество | Итого
Да | Microsoft.Web/sites | Http2xx | HTTP 2xx | Количество | Итого
Да | Microsoft.Web/sites | Http3xx | HTTP 3xx: | Количество | Итого
Да | Microsoft.Web/sites | Http401 | HTTP 401: | Количество | Итого
Да | Microsoft.Web/sites | Http403 | HTTP 403: | Количество | Итого
Да | Microsoft.Web/sites | Http404 | HTTP 404: | Количество | Итого
Да | Microsoft.Web/sites | Http406 | HTTP 406: | Количество | Итого
Да | Microsoft.Web/sites | Http4xx | HTTP 4xx: | Количество | Итого
Да | Microsoft.Web/sites | Http5xx | Ошибки HTTP-сервера: | Количество | Итого
Да | Microsoft.Web/sites | хттпреспонсетиме | Время ответа | Секунды | Среднее
Да | Microsoft.Web/sites | IoOtherBytesPerSecond | Операции ввода-вывода: прочие, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | IoOtherOperationsPerSecond | Операции ввода-вывода: прочих операций в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | IoReadBytesPerSecond | Операции ввода-вывода: чтение, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | IoReadOperationsPerSecond | Операции ввода-вывода: операций чтения в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | IoWriteBytesPerSecond | Операции ввода-вывода: запись, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | IoWriteOperationsPerSecond | Операции ввода-вывода: операций записи в секунду | Байт/с | Итого
Да | Microsoft.Web/sites | MemoryWorkingSet | рабочий набор памяти; | Байты | Среднее
Да | Microsoft.Web/sites | PrivateBytes | Байты исключительного пользования | Байты | Среднее
Да | Microsoft.Web/sites | Запросы | Запросы | Количество | Итого
Да | Microsoft.Web/sites | RequestsInApplicationQueue | Запросов в очереди приложений | Количество | Среднее
Да | Microsoft.Web/sites | Потоки | Счетчик потоков | Количество | Среднее
Да | Microsoft.Web/sites | TotalAppDomains | Всего доменов приложений | Количество | Среднее
Да | Microsoft.Web/sites | TotalAppDomainsUnloaded | Всего выгруженных доменов приложений | Количество | Среднее
Да | Microsoft.Web/sites/slots | AppConnections | Соединения | Количество | Среднее
Да | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | средний размер рабочего набора памяти; | Байты | Среднее
Да | Microsoft.Web/sites/slots | AverageResponseTime | Среднее время ответа: | Секунды | Среднее
Да | Microsoft.Web/sites/slots | BytesReceived | Входящие данные: | Байты | Итого
Да | Microsoft.Web/sites/slots | BytesSent | Выходные данные | Байты | Итого
Да | Microsoft.Web/sites/slots | CpuTime | Время ЦП: | Секунды | Итого
Да | Microsoft.Web/sites/slots | CurrentAssemblies | Текущее число сборок | Количество | Среднее
Да | Microsoft.Web/sites/slots | FunctionExecutionCount | Function Execution Count | Количество | Итого
Да | Microsoft.Web/sites/slots | FunctionExecutionUnits | Function Execution Units | Количество | Итого
Да | Microsoft.Web/sites/slots | Gen0Collections | Сборок мусора поколения 0 | Количество | Итого
Да | Microsoft.Web/sites/slots | Gen1Collections | Сборок мусора поколения 1 | Количество | Итого
Да | Microsoft.Web/sites/slots | Gen2Collections | Сборок мусора поколения 2 | Количество | Итого
Да | Microsoft.Web/sites/slots | Маркеры | Счетчик дескрипторов | Количество | Среднее
Да | Microsoft.Web/sites/slots | хеалсчеккстатус | Состояние проверки работоспособности | Количество | Среднее
Да | Microsoft.Web/sites/slots | Http101 | Http 101 | Количество | Итого
Да | Microsoft.Web/sites/slots | Http2xx | HTTP 2xx | Количество | Итого
Да | Microsoft.Web/sites/slots | Http3xx | HTTP 3xx: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http401 | HTTP 401: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http403 | HTTP 403: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http404 | HTTP 404: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http406 | HTTP 406: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http4xx | HTTP 4xx: | Количество | Итого
Да | Microsoft.Web/sites/slots | Http5xx | Ошибки HTTP-сервера: | Количество | Итого
Да | Microsoft.Web/sites/slots | хттпреспонсетиме | Время ответа | Секунды | Среднее
Да | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | Операции ввода-вывода: прочие, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | Операции ввода-вывода: прочих операций в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | IoReadBytesPerSecond | Операции ввода-вывода: чтение, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | Операции ввода-вывода: операций чтения в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | Операции ввода-вывода: запись, байт в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | Операции ввода-вывода: операций записи в секунду | Байт/с | Итого
Да | Microsoft.Web/sites/slots | MemoryWorkingSet | рабочий набор памяти; | Байты | Среднее
Да | Microsoft.Web/sites/slots | PrivateBytes | Байты исключительного пользования | Байты | Среднее
Да | Microsoft.Web/sites/slots | Запросы | Запросы | Количество | Итого
Да | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Запросов в очереди приложений | Количество | Среднее
Да | Microsoft.Web/sites/slots | Потоки | Счетчик потоков | Количество | Среднее
Да | Microsoft.Web/sites/slots | TotalAppDomains | Всего доменов приложений | Количество | Среднее
Да | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Всего выгруженных доменов приложений | Количество | Среднее
