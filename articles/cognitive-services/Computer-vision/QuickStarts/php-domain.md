---
title: Краткое руководство. Содержимое предметной области — REST, PHP
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как использовать модель предметной области для распознавания достопримечательностей с помощью API компьютерного зрения в PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 902894ccce28ee1ba05430c7e697c3ca47887795
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011118"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Краткое руководство. Распознавание содержимого предметной области с использованием REST API компьютерного зрения и PHP

Из этого краткого руководства вы узнаете, как использовать модель предметной области для поиска достопримечательностей или, при необходимости, знаменитостей на изображении в удаленном расположении с REST API компьютерного зрения. Вы можете применить модель предметной области для распознавания содержимого на изображении с помощью метода [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Необходимо установить [PHP](https://secure.php.net/downloads.php).
- Необходимо установить [Pear](https://pear.php.net).
- У вас должен быть ключ подписки для Компьютерного зрения. На странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) можно получить бесплатную пробную версию ключа. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.

## <a name="create-and-run-the-sample"></a>Создание и выполнение примера кода

Чтобы создать и запустить пример, сделайте следующее.

1. Установите пакет [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) для PHP5.
   1. Откройте окно командной строки с правами администратора.
   1. Выполните следующую команду:

      ```console
      pear install HTTP_Request2
      ```

   1. После успешной установки пакета закройте окно командной строки.

1. Скопируйте приведенный ниже код в текстовый редактор.
1. При необходимости внесите в код следующие изменения.
    1. Замените значение `subscriptionKey` своим ключом подписки.
    1. Замените значение `uriBase` URL-адресом конечной точки для метода [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) из региона Azure, где вы получили ключи подписки, если это необходимо.
    1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, анализ которого следует выполнить.
    1. При необходимости замените значение `domain` параметра запроса на значение `celebrites`, если вы хотите использовать модель предметной области `celebrities` вместо модели `landmarks`.
1. Сохраните код как файл с расширением `.php`. Например, `use-domain-model.php`.
1. Откройте окно браузера с поддержкой PHP.
1. Перетащите файл в окно браузера.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера веб-сайта и в окне браузера отобразится успешный ответ, аналогичный следующему.

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если файл больше не нужен, удалите его, а затем удалите пакет `HTTP_Request2` на PHP5. Чтобы удалить пакет, сделайте следующее:

1. Откройте окно командной строки с правами администратора.
2. Выполните следующую команду:

   ```console
   pear uninstall HTTP_Request2
   ```

3. После успешного удаления пакета закройте окно командной строки.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API компьютерного зрения, который позволяет анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
