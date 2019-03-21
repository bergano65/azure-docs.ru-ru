---
title: Краткое руководство. Анализ локального изображения — REST, cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как проанализировать локальное изображение с помощью API компьютерного зрения с cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d2591bf4aeb3ec0accf322e5fb77841fb68df3dc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889324"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-curl-in-computer-vision"></a>Краткое руководство. Анализ локального изображения с помощью REST API компьютерного зрения и cURL

Из этого краткого руководства вы узнаете, как анализировать локальное изображение с помощью REST API в службе "Компьютерное зрение", чтобы извлечь визуальные признаки. С помощью метода [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) можно извлечь визуальные функции на основе содержимого изображения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- [cURL](https://curl.haxx.se/windows).
- У вас должен быть ключ подписки для Компьютерного зрения. Получение ключа подписки описано в статье [How to obtain subscription keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="create-and-run-the-sample-command"></a>Создание и запуск примера команды

Чтобы создать и запустить пример, сделайте следующее.

1. Скопируйте приведенную ниже команду в текстовый редактор.
1. При необходимости внесите следующие изменения в команду.
    1. Замените значение `<subscriptionKey>` своим ключом подписки.
    1. Замените запрос `https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze` URL-адресом конечной точки для метода [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) из региона Azure, где вы получили ключи подписки, если это необходимо.
    1. Замените значение `<localImage>` полным путем и именем файла изображения для анализа. Например, `@C:/Pictures/ImageToAnalyze.jpg`.
    1. При необходимости измените параметр языка URL-адреса запроса (`language=en`), чтобы использовать другой поддерживаемый язык.
1. Откройте окно командной строки.
1. Вставьте команду из текстового редактора в окно командной строки и выполните команду.

```console
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary "<localImage>"
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера приложения и в окне командной строки отобразится успешный ответ, аналогичный следующему.

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
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если окно командной строки больше не нужно, закройте его и текстовый редактор.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API компьютерного зрения, который позволяет анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
