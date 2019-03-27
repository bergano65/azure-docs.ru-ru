---
title: Как использовать API обнаружения аномалий в данных временных рядов
description: Узнайте, как обнаруживать аномалии в данных, либо в пакетном режиме, либо для потоковой передачи данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473333"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Практическое руководство: Использовать API обнаружения аномалий в данных временных рядов  

[API обнаружения аномалий](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) предоставляет два метода обнаружения аномалий. Либо можно выявлять аномалии в пакетном режиме на протяжении вашего раз рядов, или как данных создается путем обнаружения аномалий состояние последней точки данных. Модель обнаружения возвращает аномалий результатов, а также ожидаемое значение каждой точки данных и аномалий верхней и нижней границы обнаружения. Эти значения можно использовать для визуализации диапазон нормальных значений и аномалий в данных.

## <a name="anomaly-detection-modes"></a>Режимы обнаружения аномалий 

API обнаружения аномалий обеспечивает обнаружение режима: пакетной и потоковой передачи.

> [!NOTE]
> Следующий запрос, URL-адреса должны быть объединены с соответствующей конечной точке для вашей подписки. Например: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Обнаружение пакета

Чтобы обнаруживать аномалии во всем пакете точек данных за определенный диапазон времени, используйте следующий URI запроса с данные временных рядов: 

`/timeseries/entire/detect`. 

Отправляя данные временных рядов за один раз, API будет создание модели с помощью всем ряду и анализ каждой точки данных с ним.  

### <a name="streaming-detection"></a>Потоковая передача обнаружения

Для непрерывно обнаружения аномалий в данных потоковой передачи, используйте следующий URI запроса с вашей последней точки данных: 

`/timeseries/last/detect'`. 

Отправляя новые точки данных при их создании, можно отслеживать данные в режиме реального времени. Модель создается с точками данных, отправляемых и API определит, если последняя точка во временных рядах аномалии.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Настройка аномалий нижнюю и верхнюю границы обнаружения

По умолчанию, верхнюю и нижнюю границы для обнаружения аномалий вычисляются с помощью `expectedValue`, `upperMargin`, и `lowerMargin`. Если требуется другой границы, мы рекомендуем применять `marginScale` для `upperMargin` или `lowerMargin`. Границы будет рассчитываться следующим образом:

|Границ  |Вычисление  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Ниже приведены примеры результата API обнаружения аномалий в разные элементы.

### <a name="example-with-sensitivity-at-99"></a>Пример с требованием к учету в 99

![Уровень конфиденциальности по умолчанию](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Пример с требованием к учету в 95

![99 чувствительности](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Пример с требованием к учету на 85

![85 чувствительности](../media/sensitivity_85.png)

## <a name="next-steps"></a>Следующие шаги

* [Что такое API обнаружения аномалий?](../overview.md)
* [Краткое руководство Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий](../quickstarts/detect-data-anomalies-csharp.md)