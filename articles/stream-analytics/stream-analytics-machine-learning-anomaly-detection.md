---
title: Обнаружение аномалий в Azure Stream Analytics
description: В этой статье объясняется, как обнаруживать аномалии с помощью Azure Stream Analytics в сочетании со службой "Машинное обучение Azure".
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 88c0aea851bcf70206b5f68d7865c487441905f6
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329899"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Обнаружение аномалий в Azure Stream Analytics

Azure Stream Analytics, доступный в облаке и в Azure IoT Edge, предлагает встроенные возможности обнаружения аномалий на основе машинного обучения, которые можно использовать для мониторинга двух наиболее часто встречающихся аномалий: временной и постоянной. С помощью функций **AnomalyDetection_SpikeAndDip** и **AnomalyDetection_ChangePoint** можно выполнять обнаружение аномалий непосредственно в задании Stream Analytics.

Модели машинного обучения предполагают наличие временных рядов с равномерной выборкой. Если временные ряды неоднородны, можно вставить шаг агрегирования с "переворачивающимся" окном перед вызовом обнаружения аномалий.

Операции обучения машины не поддерживают сезонных тенденций или несколькими вариативных корреляции в данный момент.

## <a name="model-accuracy-and-performance"></a>Точность и эффективность модели

Как правило, точность модели повышается с увеличением количества данных в скользящем окне. Данные в указанном скользящем окне обрабатываются как часть нормального диапазона значений для этого интервала времени. Модель рассматривает историю событий только через скользящее окно, чтобы проверить, является ли текущее событие аномальным. При перемещении скользящего окна старые значения исключаются из обучения модели.

Функции работают, устанавливая определенную норму, основанную на наблюдениях на этом этапе. Выбросы определяются путем сравнения с установленной нормой в пределах уровня достоверности. Размер окна должен основываться на минимальных событиях, необходимых для обучения модели нормальному поведению, чтобы при возникновении аномалии она могла его распознать.

Время отклика модели увеличивается с размером журнала, так как они нужны для сравнения с большего количества прошлых событиях. Рекомендуется включать только необходимое количество событий, чтобы повысить эффективность.

Разрывы во временных рядах могут произойти, потому что модель не получает события в определенные моменты времени. Эта ситуация разбирается по Stream Analytics, с помощью логики подстановки. Размер журнала, а также продолжительность времени для одного и того же скользящего окна используются для расчета средней скорости, с которой ожидаются события.

## <a name="spike-and-dip"></a>Пики и спады

Временные аномалии в потоке событий временных рядов называются пиками и спадами. Пики и спады можно отслеживать с помощью оператора Машинного обучения [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Пример аномалии пика и спада](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

В том же скользящем окне, если второй пик меньше первого, вычисленная оценка меньшего пика, вероятно, недостаточно значительна по сравнению с оценкой первого пика в пределах указанного уровня достоверности. Вы можете попробовать уменьшить уровень достоверности модели для обнаружения таких аномалий. Однако если предупреждений становится слишком много, можно использовать более высокий интервал достоверности.

Следующий пример запроса предполагает равномерную скорость ввода одного события в секунду в 2-минутном скользящем окне с журналом со 120 событиями. Заключительная инструкция SELECT извлекает и выводит оценку и состояние аномалии с уровнем достоверности 95 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Изменение точки

Постоянные аномалии в потоке событий временных рядов — это изменения в распределении значений в потоке событий, такие как изменения уровня и тенденции. В Stream Analytics такие аномалии обнаруживаются с помощью оператора [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics), основанного на Машинном обучении.

Постоянные изменения длятся гораздо дольше, чем пики и спады, и могут указывать на катастрофические события. Постоянные изменения обычно не видны невооруженным глазом, но их легко обнаружить с помощью оператора **AnomalyDetection_ChangePoint**.

На следующем рисунке показан пример изменения уровня:

![Пример аномалии изменения уровня](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

На следующем рисунке показан пример изменения тенденции:

![Пример аномалии изменения тенденции](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Следующий пример запроса предполагает равномерную скорость ввода одного события в секунду в 20-минутном скользящем окне с журналом с 1200 событиями. Заключительная инструкция SELECT извлекает и выводит оценку и состояние аномалии с уровнем достоверности 80 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Обнаружение аномалий с помощью машинного обучения в Azure Stream Analytics

В следующем видеоролике показано, как обнаружить аномалии в реальном времени с помощью функций машинного обучения в Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

