---
title: Метрики на основе журналов Azure Application Insights Документы Майкрософт
description: В этой статье перечислены метрики Azure Application Insights с поддерживаемыми агрегатами и размерами. Подробная информация о метриках на основе журналов включает в себя основные инструкции запроса Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664921"
---
# <a name="application-insights-log-based-metrics"></a>Метрики на основе приложений

Метрики на основе журналов Application Insights позволяют анализировать работоспособность контролируемых приложений, создавать мощные панели мониторинга и настраивать оповещения. Существует два вида метрик:

* [Метрики на основе журналов](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) за кулисами переводятся в [запросы Kusto](https://docs.microsoft.com/azure/kusto/query/) из сохраненных событий.
* [Стандартные метрики](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) хранятся в виде предварительно агрегированных временных рядов.

Поскольку *стандартные метрики* предварительно агрегируются во время сбора, они имеют более высокую производительность во время запроса. Это делает их лучшим выбором для dashboarding и в режиме реального времени оповещения. *Метрики на основе журналов* имеют больше измерений, что делает их превосходным вариантом для анализа данных и специальной диагностики. Используйте [селектор пространства имен](metrics-getting-started.md#create-your-first-metric-chart) для переключения между журналом на основе и стандартных метрик в области [исследователя метрик.](metrics-getting-started.md)

## <a name="interpret-and-use-queries-from-this-article"></a>Интерпретация и использование запросов из этой статьи

В этой статье перечислены метрики с поддерживаемыми агрегатами и размерами. Подробная информация о метриках на основе журналов включает в себя основные инструкции запроса Kusto. Для удобства каждый запрос использует значения по умолчанию для детализации времени, типа диаграммы, а иногда и измерения разделения, что упрощает использование запроса в журнале Analytics без необходимости изменения.

При графике одной и той же метрики [в исследователе метрик](metrics-getting-started.md)нет по умолчанию - запрос динамически корректируется на основе настроек диаграммы:

- Выбранный **диапазон времени** переводится в дополнительный, *где метка времени...* Например, диаграмма, показывающая данные за последние 24 часа, в запрос включена диаграмма, *где метка времени > назад (24 ч).*

- Выбранное **время детализации** ставится в окончательный *обобщение ... по пункту бен (timestamp, «зерно времени»).*

- Любые выбранные размеры **фильтра** переводятся в дополнительные, *где* есть оговорки.

- Выбранное измерение **диаграммы Сплита** переводится в дополнительное подытоживаемое свойство. Например, если вы разделите диаграмму по *местоположению*и участок, используя 5-минутное детализацию времени, *резюмируется* оговорка *обобщены ... по бен (таймштамп, 5 м), расположение*.

> [!NOTE]
> Если вы новичок в языке запросов Kusto, вы начинаете с копирования и вставки инструкций Kusto в панель запросов Log Analytics без внесения каких-либо изменений. Нажмите **Выполнить,** чтобы увидеть основную диаграмму. Когда вы начнете понимать синтаксис языка запросов, вы можете начать вносить небольшие изменения и видеть влияние ваших изменений. Изучение собственных данных — это отличный способ начать реализацию всей возможности [log Analytics](../../azure-monitor/log-query/get-started-portal.md) и [Azure Monitor.](../../azure-monitor/overview.md)

## <a name="availability-metrics"></a>Метрики доступности

Метрики в категории Доступность позволяют увидеть работоспособность вашего веб-приложения, как это наблюдается из точек по всему миру. [Назначь тесты доступности,](../../azure-monitor/app/monitor-web-app-availability.md) чтобы начать использовать любые метрики из этой категории.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Доступность (доступностьРезультаты/доступностьПроцент)
Метрика *доступности* показывает процент заедок веб-тестов, которые не обнаружили никаких проблем. Наименьшее возможное значение 0, что указывает на то, что все запуски веб-тестов не удались. Значение 100 означает, что все веб-тесты прошли критерии проверки.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|---|---|---|
|Процент|Среднее|Местоположение, имя теста|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Продолжительность теста доступности (доступностьРезультаты/продолжительность)

Метрика *продолжительности теста доступности* показывает, сколько времени потребовалось для запуска веб-теста. Для [многоступенчатых веб-тестов](../../azure-monitor/app/availability-multistep.md)метрика отражает общее время выполнения всех шагов.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|---|---|---|
|Миллисекунды|Средний, Мин, Макс|Местоположение, имя теста, результат тестирования

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Тесты доступности (доступностьРезультаты/счет)

Метрика *тестов доступности* отражает количество веб-тестов, проведенных Azure Monitor.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|---|---|---|
|Count|Count|Местоположение, имя теста, результат тестирования|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Метрики браузера

Метрики браузера собираются приложением Insights JavaScript SDK из реальных браузеров конечных пользователей. Они обеспечивают большое понимание опыта ваших пользователей с вашим веб-приложением. Показатели браузера, как правило, не отбираются, что означает, что они обеспечивают более высокую точность числа использования по сравнению с показателями на стороне сервера, которые могут быть искажены выборкой.

> [!NOTE]
> Для сбора метрик браузера ваше приложение должно быть оснащено [приложением СДК «Application Insights».](../../azure-monitor/app/javascript.md)

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Время загрузки страницы браузера (браузерТайминги/totalDuration)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|
|---|---|---|
|Миллисекунды|Средний, Мин, Макс|None|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Время обработки клиентов (браузерТимити/обработкаДлительно)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|
|---|---|---|
|Миллисекунды|Средний, Мин, Макс|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Время подключения сети загрузки страницы (браузерTimings/networkDuration)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|
|---|---|---|
|Миллисекунды|Средний, Мин, Макс|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Время отклика (браузерТайминги/receiveDuration)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|
|---|---|---|
|Миллисекунды|Средний, Мин, Макс|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Время отправки запроса (браузерТайминги/отправкаДлительно)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|
|---|---|---|
|Миллисекунды|Средний, Мин, Макс|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Показатели сбоя

Метрики в **сбоях** показывают проблемы с обработкой запросов, вызовов зависимостей и брошенных исключений.

### <a name="browser-exceptions-exceptionsbrowser"></a>Исключения браузера (исключения/браузер)

Эта метрика отражает количество исключений из кода приложения, работаемого в браузере. В метрику включены ```trackException()``` только исключения, отслеживаемые с помощью вызова API Application Insights.

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|Примечания|
|---|---|---|---|
|Count|Count|None|Версия на основе журнала использует агрегацию **Суммы**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Сбои вызовов зависимости (зависимости/неудачи)

Количество неудавшихся вызовов зависимости.

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|Примечания|
|---|---|---|---|
|Count|Count|None|Версия на основе журнала использует агрегацию **Суммы**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Исключения (исключения/счет)

Каждый раз, когда вы регистрируете исключение из Application Insights, возникает вызов [к методу trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) SDK. Метрика исключений показывает количество зарегистрированных исключений.

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|Примечания|
|---|---|---|---|
|Count|Count|Имя роли облака, пример роли облака, тип устройства|Версия на основе журнала использует агрегацию **Суммы**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Неудачные запросы (запросы/неудачные)

Количество отслеживаемых запросов серверов, которые были помечены как *неудавшийся.* По умолчанию SDK Application Insights автоматически отмечает каждый запрос сервера, который вернул код ответа HTTP 5xx или 4xx в качестве неудавшегося запроса. Эту логику можно настроить, изменив свойство *успеха* элемента телеметрии запроса в [пользовательском инициализаторе телеметрии.](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|Примечания|
|---|---|---|---|
|Count|Count|Экземпляр роли облака, имя роли облака, реальный или синтетический трафик, производительность запроса, код ответа|Версия на основе журнала использует агрегацию **Суммы**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Исключения сервера (исключения/сервер)

Эта метрика показывает количество исключений сервера.

|Единица измерения|Поддерживаемые агрегации|Предварительно агрегированные размеры|Примечания|
|---|---|---|---|
|Count|Count|Имя роли облака, экземпляр роли облака|Версия на основе журнала использует агрегацию **Суммы**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Счетчики производительности

Используйте метрики в **категории счетчиков производительности** для доступа [к счетчикам производительности системы, собранным Application Insights.](../../azure-monitor/app/performance-counters.md)

### <a name="available-memory-performancecountersavailablememory"></a>Доступная память (производительностьКонтры/доступнаЯПамять)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Коэффициент исключения (производительностьCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Время выполнения запроса HTTP (производительностьCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Скорость запроса HTTP (производительностьCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>ЗАПРОСы HTTP в очереди приложений (производительностьКонтры/запросыInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Процессор процесса (производительностьCounters/processCpuPercentage)

Метрика показывает, сколько из общей емкости процессора потребляется в процессе размещения вашего контролируемого приложения.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|
|Процент|Средний, Мин, Макс|Экземпляр облачной роли

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Скорость обработки IO (производительностьCounters/processIOBytesPerSecond)

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|
|Байты в секунду|Средний, Мин, Макс|Экземпляр облачной роли

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Обработка частных байтов (производительностьCounters/processPrivateBytes)

Количество необщей памяти, выделенной контролируемым процессом для своих данных.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|
|Байты|Средний, Мин, Макс|Экземпляр облачной роли

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Время процессора (производительностьCounters/processorCpuPercentage)

Потребление процессора *всеми* процессами, запущенными на отслеживаемом экземпляре сервера.

|Единица измерения|Поддерживаемые агрегации|Поддерживаемые размеры|
|---|---|---|
|Процент|Средний, Мин, Макс|Экземпляр облачной роли

>[!NOTE]
> Метрика времени процессора недоступна для приложений, размещенных в службах приложений Azure. Используйте метрику [процессора Process](#process-cpu-performancecountersprocesscpupercentage) для отслеживания использования процессора веб-приложений, размещенных в Службах Приложений.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Метрики сервера

### <a name="dependency-calls-dependenciescount"></a>Звонки зависимости (зависимости/счет)

Эта метрика по отношению к количеству вызовов зависимости.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Длительность зависимости (зависимости/продолжительность)

Эта метрика относится к продолжительности вызовов зависимости.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Запросы серверов (запросы/счет)

Эта метрика отражает количество входящих запросов сервера, которые были получены вашим веб-приложением.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Время отклика сервера (запросы/продолжительность)

Эта метрика отражает время, зашло у серверов для обработки входящих запросов.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Показатели использования

### <a name="page-view-load-time-pageviewsduration"></a>Время загрузки страницы (pageViews/длительность)

Эта метрика относится к времени загрузки событий PageView.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Просмотры страниц (страницыПросмотры/счет)

Количество событий PageView зарегистрировано с помощью API Исследования приложений TrackPageView()

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Сессии (сессии/счет)

Эта метрика относится к подсчету различных идонов сеанса.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Следы (следы/счет)

Подсчет отчетов, зарегистрированных с вызовом TrackTrace() Application Insights API.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Пользователи (пользователи/счет)

Количество пользователей, которые доступ к вашему приложению. На точность этой метрики может быть существенно влияние с помощью телеметрической выборки и фильтрации.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Пользователи, аутентифицированные (пользователи/аутентифицированные)

Количество пользователей, прошедших проверку подлинности в вашем приложении.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
