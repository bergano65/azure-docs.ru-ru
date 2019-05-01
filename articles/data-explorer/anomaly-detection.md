---
title: Обнаружение аномалий ряда времени и прогнозирование в обозреватель данных Azure
description: Узнайте, как анализ данных временных рядов для обнаружения аномалий и прогнозирования, с помощью обозревателя данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872010"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Обнаружение аномалий и прогнозирование в обозреватель данных Azure

Обозреватель данных Azure выполняет сбор данных телеметрии выполняющуюся облачных служб или устройствах Интернета вещей. Эти данные анализируются для анализа различных данных, например мониторинга работоспособности службы, физических рабочих процессов, тенденций использования и прогноз нагрузки. Анализ выполняется на временном ряде выбранные метрики, чтобы найти шаблон отклонение метрики по отношению к шаблону типичный обычных показателей. Обозреватель данных Azure содержит встроенную поддержку для создания, обработки и анализа несколько временных рядов. Его можно создать и проанализировать тысячи временных рядов в секундах, включение практически в реальном времени, решения и рабочие процессы мониторинга.

В этой статье подробно обозреватель данных Azure серии аномалий обнаружения и прогнозирование возможности времени. Функции применимо временных рядов основаны на модели надежный известный декомпозиции, где каждый исходный временной ряд разложить сезонных, тенденции и остались компоненты. Аномалии определяются путем выбросы остаточная компонента, хотя прогнозирование выполняется с помощью сезонных экстраполяции и компоненты отчетов о тенденциях. Реализация обозревателя данных Azure значительно расширяет модель основные декомпозиции, автоматическое сезонности, надежные выбросов анализа и векторизированную реализацию для обработки тысяч временных рядов в секундах.

## <a name="prerequisites"></a>Технические условия

Чтение [время анализа рядов в обозреватель данных Azure](/azure/data-explorer/time-series-analysis) Общие сведения о возможности ряда времени.

## <a name="time-series-decomposition-model"></a>Декомпозиция модели временных рядов

Реализация Azure в обозреватель данных собственного прогнозирование временных рядов и обнаружения аномалий использует модель хорошо известных декомпозиции. Эта модель применяется к одному временному ряду показателей, должны периодически и тенденций поведения, например трафик службы, компонент периодических сигналов и Интернета вещей периодического измерения, чтобы спрогнозировать будущие значения метрик и выявлять аномальные из них манифеста. Предположение этого процесса регрессии — отличный от ранее известные сезонных и поведение тренда, времени, серии распределяются случайным образом. Затем можно прогнозировать будущие значения метрик из сезонных и компоненты тенденций, вместе с именем базового плана и игнорировать остаточная часть. Можно также определить аномальные значения, основанных на анализе выбросов, с помощью только остаточная часть.
Чтобы создать модель декомпозиции, используйте функцию [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). `series_decompose()` Функция принимает набор временных рядов и автоматически разбивает каждый временной ряд, чтобы его сезонных, тренда, residual и базовых компонентов. 

Например можно разложить трафика внутренних веб-службы с помощью следующего запроса:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Разбор временного ряда](media/anomaly-detection/series-decompose-timechart.png)

* Исходный временных рядов имеет метку **num** (выделены красным цветом). 
* Процесс начинается с автоматическое обнаружение сезонности, используя функцию [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) и извлекает **сезонных** шаблон (фиолетовым цветом).
* Сезоне вычитается из исходного ряда времени и Линейная регрессия выполняется с помощью функции [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) найти **тренда** компонент (светло-синий).
* Функция вычитает тренд, и остаток **остаточная** компонент (зеленым цветом).
* Наконец, функция добавляет сезонных и компоненты для создания отчетов о тенденциях **базовых показателей** (синим цветом).

## <a name="time-series-anomaly-detection"></a>Обнаружение аномалий ряда времени

Функция [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) находит аномальных точки на наборе временных рядов. Эта функция вызывает `series_decompose()` для построения модели декомпозиции, а затем выполняет [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) остаточная компонента. `series_outliers()` Вычисляет показателей аномалий для каждой точки остаточная компонента, используя Тьюки его граница теста. Показателей аномалий больше 1,5 или меньше – 1,5 указывать умеренное аномалий увеличение числа прерванных игр или отказаться от соответственно. Оценки аномалий выше 3.0 или ниже: -3.0 указывают строгого аномалий. 

Следующий запрос позволяет обнаруживать аномалии в внутренний веб-трафик службы:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Обнаружение аномалий ряда времени](media/anomaly-detection/series-anomaly-detection.png)

* Исходный временных рядов (выделены красным цветом). 
* Базовый план (сезонных + тренда) компонент (синим цветом).
* Аномальные точки (фиолетовым цветом) на основе исходного временных рядов. Аномальные точек значительно отличаются от ожидаемого базовые показатели.

## <a name="time-series-forecasting"></a>Прогнозирование временных рядов

Функция [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) Прогнозирует будущие значения из набора временных рядов. Эта функция вызывает `series_decompose()` для сборки развернутого модели, а затем для каждого временного ряда Экстраполирует компонента базовых показателей в будущем.

Следующий запрос позволяет прогнозировать следующей неделе веб-службы трафика:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Прогнозирование временных рядов](media/anomaly-detection/series-forecasting.png)

* Первоначальные метрика (выделены красным цветом). Будущие значения отсутствуют и значение 0, по умолчанию.
* Экстраполируйте компонента базовых показателей (синим цветом) для прогнозирования значений на следующей неделе.

## <a name="scalability"></a>Масштабируемость

Синтаксис языка запросов Azure обозреватель данных позволяет обрабатывать несколько временных рядов на один вызов. Уникальный оптимизированная позволяет высокую производительность, что очень важно для эффективной аномалий и прогнозирования при наблюдении за тысячи счетчиков в практически сценариев в реальном времени.

В следующем запросе показано обработки три временных рядов одновременно:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Масштабируемость ряда времени](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Сводка

В этом документе описаны собственных функций обозревателя данных Azure для обнаружения аномалий ряда времени и прогнозирование, что значительно повышает декомпозиции базовые модели, на котором он основан. Разложить каждого исходного временного ряда, обнаружения, аномалий и прогнозирование выполняется. Время функции обнаружения и прогнозирования в аномалий рядов используются для практически в реальном времени сценариев мониторинга, например обнаружения сбоев, прогнозируемого обслуживания и спроса и загрузить прогнозирования.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [машинного обучения возможности](/azure/data-explorer/machine-learning-clustering) в обозреватель данных Azure.