---
title: Краткое руководство. Распознавание эмоций лица на изображении — API распознавания эмоций с использованием Java
description: Информация и примеры кода, которые помогут быстро приступить к работе с API распознавания эмоций на языке Java для Android.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: a3250771c51495890c54060dc46b8bd88a8f54af
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228684"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Краткое руководство. Создание приложения для распознавания эмоций лица на изображении

> [!IMPORTANT]
> 15 февраля 2019 г. API распознавания эмоций будет отмечен как нерекомендуемый. Сейчас функция распознавания эмоций является общедоступной в составе [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/). 

Эта статья содержит информацию и пример кода, которые помогут быстро приступить к работе с [методом распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) в клиентской библиотеке API распознавания эмоций для Android. В этом примере показано, как с помощью Java распознавать эмоции людей.

## <a name="prerequisites"></a>Предварительные требования
* Скачайте пакет SDK API распознавания эмоций на Java для Android [здесь](https://github.com/Microsoft/Cognitive-emotion-android).
* Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-java-for-android-example-request"></a>Пример запроса API распознавания эмоций на Java для Android

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
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
