---
title: Руководство. Извлечение сведений об изображении с помощью API Bing для поиска изображений и C#
titleSuffix: Azure Cognitive Services
description: Воспользуйтесь этой статьей, чтобы создать приложение C#, извлекающее сведения об изображении с помощью API Bing для поиска изображений.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: tutorial
ms.date: 9/14/2018
ms.author: aahi
ms.openlocfilehash: 36996ebed25d159cdc2a1908b5c9f1f5447c823f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259772"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Руководство. Извлечение сведений об изображении с помощью API Bing для поиска изображений и C#

Существует несколько [конечных точек](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint), доступ к которым можно получить с помощью API Bing для поиска изображений. Конечная точка `/details` принимает запрос POST с изображением и может возвращать различные сведения об изображении. Это приложение C# отправляет изображение с помощью этого API и отображает сведения, возвращенные Bing, которые являются объектами JSON, например следующие:

![[Результаты JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
> * Использование конечной точки для поиска изображений `/details` в запросе `POST`.
> * Указание заголовков для запроса.
> * Использование параметров URL-адреса для указания результатов.
> * Отправка данных изображений и отправка запроса `POST`.
> * Печать результатов JSON в консоль.

Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Составление запроса на поиск сведений об изображении

Ниже приведена конечная точка `/details`, принимающая запросы POST с данными изображения в тексте запроса.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

При создании URL-адреса запроса на поиск параметр `modules` указывает типы сведений, которые будут содержаться в результатах, с учетом конечной точки выше:

* `modules=All`
* `modules=RecognizedEntities` (люди или места, видимые в изображении).

Если в запросе POST указать `modules=All`, то полученный текст JSON будет содержать такой список:

* `bestRepresentativeQuery` — запрос Bing, который возвращает изображения, похожие на отправленное изображение.
* `detectedObjects` — объекты, найденные на изображении.
* `image` — метаданные изображения.
* `imageInsightsToken` — токен для последующих запросов GET, которые получают `RecognizedEntities` (люди или места на изображении) с изображения.
* `imageTags` — теги для изображения.
* `pagesIncluding` — веб-страницы, содержащие данное изображение.
* `relatedSearches` — поисковые запросы по сведениям на изображении.
* `visuallySimilarImages` — похожие изображения в Интернете.

Укажите `modules=RecognizedEntities` в запросе POST, чтобы получить только тот токен `imageInsightsToken`, который может использоваться в последующем запросе GET для определения людей или места на изображении.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Создание объекта WebClient и установка заголовков для запроса API

Создайте объект `WebClient` и задайте заголовки. Во всех запросах к API-интерфейсам поиска Bing необходимо указывать параметр `Ocp-Apim-Subscription-Key`. В запросе `POST` на отправку изображения необходимо также указать `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Отправка изображения и отображение результатов

Метод `UpLoadFile()` класса `WebClient` форматирует данные для запроса `POST`, включая форматирование `RequestStream` и вызов `HttpWebRequest`.

Вызовите `WebClient.UpLoadFile()`, указав конечную точку `/details` и файл изображения для отправки. Используйте ответ JSON для инициализации экземпляра структуры `SearchResult` и сохраните ответ.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Затем этот ответ JSON можно вывести в консоли.

## <a name="use-an-image-insights-token-in-a-request"></a>Использование токена аналитических сведений изображения в запросе

Для использования токена `ImageInsightsToken`, возвращенного с результатами запроса `POST`, его можно добавить в запрос `GET`. Например: 

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Если в изображении удается идентифицировать людей или места, то этот запрос возвращает сведения о них.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>См. также

* [Справочник по API Bing для поиска изображений](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
