---
title: Краткое руководство по использованию клиентской библиотеки Поиска изображений Bing (Java)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 4656d8116ddd2e618adad97aeea576e88bc8785f
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899389"
---
Воспользуйтесь сведениями из краткого руководства и выполните первый поиск изображения с помощью клиентской библиотеки Поиска изображений Bing. Эта библиотека является оболочкой для API и содержит те же функции. Это простое приложение Java отправляет запрос на поиск изображения, анализирует ответ JSON и отображает URL-адрес первого возвращенного изображения.

Исходный код, используемый в данном примере, вместе с дополнительной обработкой ошибок и аннотациями можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart).

## <a name="prerequisites"></a>Предварительные требования

Последняя версия [пакета средств разработки Java (JDK)](https://aka.ms/azure-jdks).

Установите зависимости клиентской библиотеки Поиска изображений Bing с помощью Maven, Gradle или другой системы управления зависимостями. Для файла POM Maven требуется следующее объявление:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте проект Java в избранной интегрированной среде разработки или редакторе и добавьте следующие строки импорта в реализацию класса:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. В методе main создайте переменные для ключа подписки и условия поиска. Затем создайте экземпляр клиента API Bing для поиска изображений.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Отправка поискового запроса к API

1. С помощью `bingImages().search()` отправьте HTTP-запрос, содержащий поисковой запрос. Сохранить ответ в виде `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Синтаксический анализ и просмотр результата

Выполните синтаксический анализ результатов изображений, возвращенных в ответе.
Если в ответе содержатся результаты поиска, сохраните первый результат и распечатайте такие сведения, как URL-адрес эскиза, исходный URL-адрес и общее количество возвращенных изображений.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>См. также раздел

* [Что такое API Bing для поиска изображений?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Попробуйте API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Примеры Java для пакета SDK в Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Документация по службам Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
