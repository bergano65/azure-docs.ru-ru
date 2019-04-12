---
title: Как использовать API Anomaly Finder в Microsoft Cognitive Services с помощью Ruby | Документация Майкрософт
description: Информация и примеры кода, которые помогут вам быстро приступить к работе с API Anomaly Finder и Ruby в Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 020c957baf6673365d64c613bd908a440bc3d05c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735039"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Использование API Anomaly Finder с Ruby

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к работе с API Anomaly Finder и Ruby для получения результатов обнаружения аномалий в данных временных рядов.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Получение точек аномалий с помощью API Anomaly Finder и Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Пример данных временных рядов
Ниже приведен пример точек данных по временным рядам.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Пример анализа данных и получения точек аномалий с помощью Ruby

Для использования этого примера сделайте следующее.

1. Установите [rest-client](https://github.com/rest-client/rest-client), выполнив команду gem install rest-client.
2. Сохраните приведенный ниже код как RB-файл.
3. Замените значение `[YOUR_SUBSCRIPTION_KEY]` действительным ключом подписки.
4. Замените `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` примером собственных точек данных.
5. Выполните запрос и проверьте ответ.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Пример ответа

Успешный ответ будет возвращен в формате JSON. Пример ответа приведен ниже.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
