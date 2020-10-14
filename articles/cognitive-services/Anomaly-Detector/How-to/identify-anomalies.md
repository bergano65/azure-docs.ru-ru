---
title: Использование API детектора аномалий в данных временных рядов
titleSuffix: Azure Cognitive Services
description: Узнайте, как обнаруживать аномалии в данных как в виде пакета, так и в потоковой передаче данных.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: b2cd5e32503953de874ab470ca3f9413d2b37d59
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014653"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Как использовать API детектора аномалии для данных временных рядов  

[API детектора аномалий](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) предоставляет два метода обнаружения аномалий. Аномалии можно определить как пакет в течение временных рядов или по мере создания данных, обнаруживая состояние аномалии последней точки данных. Модель обнаружения возвращает результаты аномалий вместе с ожидаемым значением точки данных, а также верхние и нижние границы обнаружения аномалий. Эти значения можно использовать для визуализации диапазона нормальных значений и аномалий в данных.

## <a name="anomaly-detection-modes"></a>Режимы обнаружения аномалий 

API детектора аномалий предоставляет режимы обнаружения: пакетная и потоковая передача.

> [!NOTE]
> Следующие URL-адреса запросов должны быть объединены с соответствующей конечной точкой для подписки. Например: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Пакетное обнаружение

Чтобы обнаружить аномалии в пакете точек данных в течение заданного диапазона времени, используйте следующий URI запроса с данными временных рядов: 

`/timeseries/entire/detect`. 

Отправляя данные временных рядов одновременно, API создаст модель, используя весь ряд, и проанализирует каждую точку данных.  

### <a name="streaming-detection"></a>Обнаружение потоковой передачи

Чтобы постоянно обнаруживать аномалии в потоковой передаче данных, используйте следующий URI запроса с последней точкой данных: 

`/timeseries/last/detect'`. 

Отправляя новые точки данных по мере их создания, можно отслеживать данные в режиме реального времени. Будет создана модель с отправленными точками данных, и API определит, является ли последняя точка во временном ряде неаномалией.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Настройка границ обнаружения нижних и верхних аномалий

По умолчанию верхние и нижние границы для обнаружения аномалий вычисляются с помощью `expectedValue` , `upperMargin` и `lowerMargin` . Если требуются разные границы, рекомендуется применить `marginScale` к `upperMargin` или `lowerMargin` . Границы будут вычисляться следующим образом:

|Граница  |Вычисление  |
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

## <a name="next-steps"></a>Next Steps

* [Общие сведения об API Детектора аномалий](../overview.md)
* [Краткое руководство. Обнаружение аномалий в данных временных рядов с помощью Детектор аномалий API REST](../quickstarts/detect-data-anomalies-csharp.md)
