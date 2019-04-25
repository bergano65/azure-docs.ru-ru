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
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 32fb37e2e81d3f876a29c32bf56b226452a435bf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007820"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-nodejs-in-computer-vision"></a>Краткое руководство. Краткое руководство по созданию эскиза с помощью API компьютерного зрения, REST API и Node.js

Из этого краткого руководства вы узнаете, как создать эскиз изображения с помощью REST API компьютерного зрения. Метод [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) позволяет создать эскиз изображения. Вы можете указать нужную высоту и ширину. При этом пропорции могут отличаться от пропорций исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Установите платформу [Node.js](https://nodejs.org) 4.x или более поздней версии.
- Установите [npm](https://www.npmjs.com/).
- У вас должен быть ключ подписки для Компьютерного зрения. На странице [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) (Пробная версия Cognitive Services) можно получить ключ бесплатной пробной версии. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.

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
1. При необходимости внесите в код следующие изменения.
    1. Замените значение `subscriptionKey` своим ключом подписки.
    1. Замените значение `uriBase` URL-адресом конечной точки для метода [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) из региона Azure, где вы получили ключи подписки, если это необходимо.
    1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, анализ которого следует выполнить.
1. Сохраните код как файл с расширением `.js`. Например, `get-thumbnail.js`.
1. Откройте окно командной строки.
1. В командной строке запустите файл с помощью команды `node`. Например, `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail';

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
