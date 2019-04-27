---
title: Использование API Anomaly Finder с cURL — Microsoft Cognitive Services | Документация Майкрософт
description: Сведения, которые помогут вам быстро приступить к работе с API Anomaly Finder с использованием cURL в Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7a4996c83d57b34fcfcff43650b21359acb4e65e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094907"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Использование API Anomaly Finder с cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к работе с API Anomaly Finder и cURL для получения результатов обнаружения аномалий в данных временных рядов.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Получение точек аномалий с помощью API Anomaly Finder и cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Пример данных временных рядов

Ниже приведен пример точек данных по временным рядам.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Пример анализа данных и получения точек аномалий с помощью cURL

Для использования этого примера сделайте следующее.

1. Замените значение `[YOUR_SUBSCRIPTION_KEY]` действительным ключом подписки.
2. Вместо `[YOUR_REGION]` укажите регион, в котором вы получили ключи подписки.
3. Замените `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` примером собственных точек данных.
4. Выполните запрос и проверьте ответ.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Пример ответа
Успешный ответ будет возвращен в формате JSON. Пример ответа приведен ниже: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
