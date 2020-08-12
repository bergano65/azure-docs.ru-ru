---
title: Краткое руководство. Анализ удаленно хранимого изображения с помощью REST API и Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как проанализировать изображение с помощью API компьютерного зрения на Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: d6962ae364c8f86d5069c9ec47eed6ffc5d2d419
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835006"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Краткое руководство. Удаленный анализ изображения с помощью REST API "Компьютерное зрение" и Node.js

Из этого краткого руководства вы узнаете, как анализировать хранимое удаленно изображение с помощью REST API Компьютерного зрения и Node.js для извлечения визуальных признаков. С помощью метода [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) можно извлечь визуальные функции на основе содержимого изображения.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* [Node. js](https://nodejs.org) 4.x или более поздней версии. 
* [npm](https://www.npmjs.com/) 
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="создайте ресурс Компьютерного зрения"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Компьютерного зрения потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
* [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.

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
1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, анализ которого следует выполнить.
1. При необходимости замените значение параметра запроса `language` на другой язык.
1. Сохраните код как файл с расширением `.js`. Например, `analyze-image.js`.
1. Откройте окно командной строки.
1. В командной строке запустите файл с помощью команды `node`. Например, `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.0/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
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
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
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

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с API-интерфейсами Компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст.

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
