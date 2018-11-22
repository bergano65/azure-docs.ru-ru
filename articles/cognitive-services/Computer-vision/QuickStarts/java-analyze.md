---
title: Краткое руководство. Анализ удаленного изображения c помощью службы "Компьютерное зрение" (REST, Java)
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как проанализировать изображение с помощью API компьютерного зрения на Java.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 2503d8cda05787885935ffab1be6b1efd85ad0cd
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852118"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-java-in-computer-vision"></a>Краткое руководство. Анализ удаленного изображения с помощью REST API и Java в службе "Компьютерное зрение"

Из этого краткого руководства вы узнаете, как анализировать удаленное изображение с помощью REST API в API компьютерного зрения, чтобы извлечь визуальные признаки. С помощью метода [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) можно извлечь визуальные функции на основе содержимого изображения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- У вас должна быть установлена платформа [Java&trade;, комплект разработчика Java, выпуск "Стандартный" версии 7 или 8](https://aka.ms/azure-jdks) (JDK 7 или 8).
- У вас должен быть ключ подписки для Компьютерного зрения. Получение ключа подписки описано в статье [How to obtain subscription keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="create-and-run-the-sample-application"></a>Создание и запуск примера приложения

Чтобы создать и запустить пример, сделайте следующее.

1. Создайте проект Java в используемой вами интегрированной среде разработки или редакторе. Если этот параметр доступен, создайте проект Java из шаблона приложения командной строки.
1. Импортируйте следующие библиотеки в свой проект Java. Если вы используете Maven — его координаты предоставляются для каждой библиотеки.
   - [HTTP-клиент Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [HTTP-ядро Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Библиотека JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Добавьте следующие операторы `import` в файл, содержащий общий класс `Main` для вашего проекта.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Замените общий класс `Main` следующим кодом, а затем внесите в него следующие изменения (там, где это необходимо).
   1. Замените значение `subscriptionKey` своим ключом подписки.
   1. Замените значение `uriBase` URL-адресом конечной точки для метода [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) из региона Azure, где вы получили ключи подписки, если это необходимо.
   1. При необходимости замените значение `imageToAnalyze` URL-адресом другого изображения, анализ которого следует выполнить.
1. Сохраните, а затем создайте проект Java.
1. Если вы используете интегрированную среду разработки, запустите `Main`. В противном случае откройте окно командной строки и затем используйте команду `java` для выполнения скомпилированного класса. Например, `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера приложения и в окне консоли отобразится успешный ответ, аналогичный следующему.

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если проект Java больше не нужен, удалите его вместе со скомпилированным классом и импортированными библиотеками.

## <a name="next-steps"></a>Дополнительная информация

Изучите приложение Java Swing, которое использует API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных компонентов изображения, включая лица. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Руководство по использованию API компьютерного зрения для Java](../Tutorials/java-tutorial.md)
