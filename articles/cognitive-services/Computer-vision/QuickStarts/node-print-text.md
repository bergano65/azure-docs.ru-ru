---
title: Краткое руководство. Извлечение печатного текста — REST, Node.js
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как, используя API компьютерного зрения, извлекать печатный текст из изображения с помощью Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 113cfc4b6c7130ec407251d6978ce4119c476d47
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141320"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-nodejs"></a>Краткое руководство. Извлечение печатного текста (OCR) с помощью REST API "Компьютерное зрение" и Node.js

Из этого краткого руководства вы узнаете, как извлекать печатный текст с оптическим распознаванием символов из изображения с помощью REST API компьютерного зрения. С помощью метода [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) можно определить печатный текст на изображении и извлечь распознанные знаки в поток знаков, пригодный для машинной обработки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Установите платформу [Node.js](https://nodejs.org) 4.x или более поздней версии.
- Установите [npm](https://www.npmjs.com/).
- У вас должен быть ключ подписки для Компьютерного зрения. На странице [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) (Пробная версия Cognitive Services) можно получить ключ бесплатной пробной версии. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ. Затем [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для строки ключа и конечной точки службы с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.

## <a name="create-and-run-the-sample"></a>Создание и выполнение примера кода

Чтобы создать и запустить пример, сделайте следующее.

1. Установите пакет npm [`request`](https://www.npmjs.com/package/request).
   1. Откройте окно командной строки с правами администратора.
   1. Выполните следующую команду:

      ```console
      npm install request
      ```

   1. После успешной установки пакета закройте окно командной строки.

1. Скопируйте приведенный ниже код в текстовый редактор.
1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, из которого вы хотите извлечь печатный текст.
1. Сохраните код как файл с расширением `.js`. Например, `get-printed-text.js`.
1. Откройте окно командной строки.
1. В командной строке запустите файл с помощью команды `node`. Например, `node get-printed-text.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.0/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера и в окне командной строки отобразится успешный ответ, аналогичный следующему.

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если файл больше не нужен, удалите его, а затем удалите пакет `request` npm. Чтобы удалить пакет, сделайте следующее:

1. Откройте окно командной строки с правами администратора.
2. Выполните следующую команду:

   ```console
   npm uninstall request
   ```

3. После успешного удаления пакета закройте окно командной строки.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API компьютерного зрения, который позволяет анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
