---
title: Краткое руководство. Создание эскиза — REST, Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью API компьютерного зрения и Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ac97ae1d3eb260a9e0c70d3f831efb8b56a5c29d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973821"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Краткое руководство. Создание эскиза с помощью REST API "Компьютерное зрение" и Node.js

В этом кратком руководстве описано, как создать эскиз изображения с помощью REST API Компьютерного зрения. Метод [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) позволяет создать эскиз изображения. Вы можете указать нужную высоту и ширину. При этом пропорции могут отличаться от пропорций исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

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
1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, анализ которого следует выполнить.
1. Сохраните код как файл с расширением `.js`. Например, `get-thumbnail.js`.
1. Откройте окно командной строки.
1. В командной строке запустите файл с помощью команды `node`. Например, `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
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
});
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ возвращается в виде двоичных данных, которые представляют данные изображения для эскиза. Если запрос завершается сбоем, ответ отображается в окне консоли. Ответ на невыполненный запрос содержит код ошибки и сообщение с описанием проблемы.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API компьютерного зрения, который позволяет анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
