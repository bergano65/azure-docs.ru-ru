---
title: Использование API детектора аномалий в данных временных рядов
titleSuffix: Azure Cognitive Services
description: Узнайте, как обнаруживать аномалии в данных как в виде пакета, так и в потоковой передаче данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: c7b3d9b66d74f16dc0938c888456d673b9cd4b77
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882887"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Практическое руководство. Использование API детектора аномалий в данных временных рядов  

[API детектора аномалий](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) предоставляет два метода обнаружения аномалий. Аномалии можно определить как пакет в течение временных рядов или по мере создания данных, обнаруживая состояние аномалии последней точки данных. Модель обнаружения возвращает результаты аномалий вместе с ожидаемым значением точки данных, а также верхние и нижние границы обнаружения аномалий. Эти значения можно использовать для визуализации диапазона нормальных значений и аномалий в данных.

## <a name="anomaly-detection-modes"></a>Режимы обнаружения аномалий 

API детектора аномалий предоставляет режимы обнаружения: пакетная и потоковая передача.

> [!NOTE]
> Следующие URL-адреса запросов должны быть объединены с соответствующей конечной точкой для подписки. Например: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Пакетное обнаружение

Чтобы обнаружить аномалии в пакете точек данных в течение заданного диапазона времени, используйте следующий URI запроса с данными временных рядов: 

`/timeseries/entire/detect`. 

Отправляя данные временных рядов одновременно, API создаст модель, используя весь ряд, и проанализирует каждую точку данных.  

### <a name="streaming-detection"></a>Обнаружение потоковой передачи

Чтобы постоянно обнаруживать аномалии в потоковой передаче данных, используйте следующий URI запроса с последней точкой данных: 

`/timeseries/last/detect'`. 

Отправляя новые точки данных по мере их создания, можно отслеживать данные в режиме реального времени. Будет создана модель с отправленными точками данных, и API определит, является ли последняя точка во временном ряде неаномалией.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Настройка границ обнаружения нижних и верхних аномалий

По умолчанию верхние и нижние границы для обнаружения аномалий вычисляются `expectedValue`с `upperMargin`помощью, `lowerMargin`и. Если требуются разные границы, рекомендуется применить `marginScale` к `upperMargin` или `lowerMargin`. Границы будут вычисляться следующим образом:

|Границ  |Вычисление  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

В следующих примерах показан результат API детектора аномалий в разных включая учет.

### <a name="example-with-sensitivity-at-99"></a>Пример с учетом чувствительности в 99

![Уровень конфиденциальности по умолчанию](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Пример с учетом чувствительности в 95

![99. чувствительность](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Пример с учетом чувствительности в 85

![85. чувствительность](../media/sensitivity_85.png)

## <a name="next-steps"></a>Следующие шаги

* [Что такое API детектора аномалий?](../overview.md)
* [Краткое руководство Обнаружение аномалий в данных временных рядов с помощью Детектор аномалий API REST](../quickstarts/detect-data-anomalies-csharp.md)
