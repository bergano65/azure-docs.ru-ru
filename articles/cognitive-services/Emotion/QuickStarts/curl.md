---
title: Краткое руководство по распознаванию эмоций лица на изображении — API распознавания эмоций, cURL
titlesuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API распознавания эмоций с использованием cURL.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dfdaa89c9d29e419539f385f601dc7f264bf838e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237081"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Краткое руководство по созданию приложения для распознавания эмоций лица на изображении

> [!IMPORTANT]
> API распознавания эмоций будет объявлен устаревшим 15 февраля 2019 г. Сейчас функция распознавания эмоций является общедоступной в составе [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/).

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к распознаванию эмоций одного человека или нескольких людей на изображении с помощью cURL и [метода API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

## <a name="prerequisite"></a>Предварительные требования
* Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-curl-example-request"></a>Пример запроса cURL для распознавания эмоций

> [!NOTE]
> В вызове REST необходимо использовать то же расположение, что и для получения ключей подписки. Например, если вы получили ключи подписки в регионе "Центрально-западная часть США", в URL-адресе ниже вместо westus укажите westcentralus.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Пример ответа на распознавание эмоций
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
