---
title: Краткое руководство. Распознавание эмоций лица на изображении — API распознавания эмоций с использованием PHP
titlesuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API распознавания эмоций с использованием PHP.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 3acf47ee26974ddff4f6063eef95bb29be61fce3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215501"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Краткое руководство. Создание приложения для распознавания эмоций лица на изображении

> [!IMPORTANT]
> 15 февраля 2019 г. API распознавания эмоций будет отмечен как нерекомендуемый. Сейчас функция распознавания эмоций является общедоступной в составе [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/). 

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к распознаванию эмоций одного человека или нескольких людей на изображении с помощью PHP и [метода API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

## <a name="prerequisite"></a>Предварительные требования
* Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-php-example-request"></a>Пример запроса PHP для распознавания эмоций

Измените URL-адрес REST, указав расположение, из которого получены ключи подписки. Замените текст запроса на URL-адрес тестируемого изображения, а значение Ocp-Apim-Subscription-Key — на действующий ключ подписки.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

## <a name="recognize-emotions-sample-response"></a>Пример ответа API распознавания эмоций
При успешном вызове возвращается массив записей лиц и связанные с ними оценки эмоций, отсортированные по убыванию в зависимости от размера прямоугольника с лицом. Пустой ответ указывает на то, что лица не обнаружены. Запись об эмоции содержит следующие поля:
* faceRectangle — расположение прямоугольника с лицом на изображении;
* scores — результаты оценки эмоций для каждого лица на изображении.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
