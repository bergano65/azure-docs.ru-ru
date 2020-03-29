---
title: Как использовать API-детектор аномалий на данных временных рядов
titleSuffix: Azure Cognitive Services
description: Узнайте, как обнаружить аномалии в данных как пакет, либо на потоковых данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840220"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Как: Используйте API-детектор аномалий на данных временных рядов  

[API детектора аномалий](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) предоставляет два метода обнаружения аномалий. Вы можете обнаружить аномалии как пакет на протяжении всей серии времен, либо по мере того, как ваши данные генерируются путем обнаружения состояния аномалии последней точки данных. Модель обнаружения возвращает результаты аномалий вместе с ожидаемым значением каждой точки данных, а также верхними и нижними границами обнаружения аномалий. эти значения можно использовать для визуализации диапазона нормальных значений и аномалий в данных.

## <a name="anomaly-detection-modes"></a>Режимы обнаружения аномалий 

API детектора аномалий обеспечивает режимы обнаружения: пакетные и потоковые.

> [!NOTE]
> Следующие URL-адреса запроса должны быть объединены с соответствующей точкой отбытого пункта для вашей подписки. Например: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Пакетное обнаружение

Для обнаружения аномалий на протяжении всей партии точек данных в течение заданного диапазона времени используйте следующий запрос URI с данными временных рядов: 

`/timeseries/entire/detect`. 

Отправив данные временных рядов сразу, API будет генерировать модель, используя всю серию, и анализировать каждую точку данных с ним.  

### <a name="streaming-detection"></a>Обнаружение потоковой передачи

Чтобы постоянно обнаруживать аномалии потоковых данных, используйте следующий запрос URI с последней точкой данных: 

`/timeseries/last/detect'`. 

Отправляя новые точки данных при их генерации, вы можете отслеживать данные в режиме реального времени. Модель будет сгенерирована с отправляемыми точками данных, и API определит, является ли последняя точка в временной серии аномалией.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Корректировка границ обнаружения нижних и верхних аномалий

По умолчанию верхние и нижние границы для `expectedValue` `upperMargin`обнаружения `lowerMargin`аномалий рассчитываются с использованием, и . Если вам требуются различные границы, `upperMargin` `lowerMargin`мы рекомендуем применить `marginScale` к или . Границы будут рассчитаны следующим образом:

|Граница  |Вычисление  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Следующие примеры показывают результат API aPI anomaly Detector при различной чувствительности.

### <a name="example-with-sensitivity-at-99"></a>Пример с чувствительностью в 99

![Уровень конфиденциальности по умолчанию](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Пример с чувствительностью в 95

![99 Чувствительность](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Пример с чувствительностью на 85

![85 Чувствительность](../media/sensitivity_85.png)

## <a name="next-steps"></a>Next Steps

* [Что такое API детектора аномалий?](../overview.md)
* [Быстрый запуск: Обнаружение аномалий в данных временных рядов с помощью API Anomaly Detector REST](../quickstarts/detect-data-anomalies-csharp.md)
