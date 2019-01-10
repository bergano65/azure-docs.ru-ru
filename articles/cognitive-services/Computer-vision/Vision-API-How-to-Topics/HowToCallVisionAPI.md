---
title: 'Пример: вызов API анализа изображений. API компьютерного зрения'
titlesuffix: Azure Cognitive Services
description: Узнайте, как вызывать API компьютерного зрения с помощью REST в Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 9520d4bcec0e170700aacc5ef4bc69100e333af1
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581714"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Пример: вызов API компьютерного зрения

В этом руководстве показано, как вызывать API компьютерного зрения с помощью REST. Примеры написаны как на языке C# с помощью клиентской библиотеки API компьютерного зрения, так и виде вызовов HTTP POST или GET. Мы сосредоточимся на следующем.

-   Получение "тегов", "описания" и "категорий".
-   Получение "информации об определенной области" (знаменитости).

### <a name="Prerequisites">Предварительные требования</a> 
URL-адрес или путь к локально сохраненному изображению.
  * Поддерживаемые методы ввода: двоичный файл изображения в формате RAW в форме приложения или потока октетов или URL-адреса изображения.
  * Поддерживаемые форматы изображения: JPEG, PNG, GIF, BMP.
  * Размер файла изображения: меньше 4 МБ.
  * Размер изображения: больше 50 x 50 пикселей.
  
В приведенных ниже примерах демонстрируются следующие возможности.

1. Анализ изображения и получение массива возвращаемых тегов и описания.
2. Анализ изображения с помощью модели предметной области (в частности, модель "знаменитости") и получение соответствующего результата в перенастраиваемом JSON.

Функции разбиваются на:

  * **Вариант 1.** Ограниченный анализ — анализ только конкретной модели.
  * **Вариант 2.** Расширенный анализ — анализ для предоставления дополнительных сведений на основе [таксономии с 86 категориями](../Category-Taxonomy.md).
  
### <a name="Step1">Шаг 1. Авторизация вызова API</a> 
Каждый вызов API компьютерного зрения требует ключ подписки. Этот ключ должен быть либо передан через параметр строки запроса, либо указан в заголовке запроса. 

Чтобы получить ключ подписки, см. раздел [Как получить ключи подписки](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** В качестве примера API компьютерного зрения см. приведенную ниже передачу ключа подписки через параметр строки запроса.

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Ключ подписки также можно указать в заголовке запроса HTTP.

```ocp-apim-subscription-key: <Your subscription key>```

**3.** При использовании клиентской библиотеки ключ подписки передается через конструктор класса VisionServiceClient.

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Шаг 2. Передача изображения в службу API компьютерного зрения и получение тегов, описаний и знаменитостей</a>
Простой способ выполнения вызовов API компьютерного зрения — передача изображения напрямую. Для этого нужно отправить запрос "POST" с типом содержимого потока приложений или октетов вместе с данными, считываемыми с изображения. Для "тегов" и "описания" этот метод отправки будет одинаковым для всех вызовов API компьютерного зрения. Единственным отличием будут параметры запроса пользователя. 

Вот как получить "теги" и "описание" для данного изображения.

**Вариант 1.** Получение списка тегов (Tags) и одного описания (Description)
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```
**Параметр 2**. Получить только список "тегов" или список "описаний".

###### <a name="tags-only"></a>Только теги.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Только описания.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Вот как можно получить анализ определенной области (в нашем случае для знаменитостей).

**Вариант 1.** Ограниченный анализ — анализ только конкретной модели.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Для этого параметра все остальные параметры запроса {visualFeatures, details} недопустимы. Чтобы увидеть все поддерживаемые модели, используйте следующее. 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Вариант 2.** Расширенный анализ — анализ для предоставления дополнительных сведений на основе [таксономии с 86 категориями](../Category-Taxonomy.md).

В приложениях, где пользователи хотят получить анализ обычного изображения с дополнительными сведениями из одной или нескольких моделей, предназначенных для определенных сфер, расширьте API версии 1 с помощью параметра модели запроса.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
При вызове этого метода сначала необходимо вызвать классификатор 86 категории. Если какие-либо категории соответствуют известным или сопоставленным моделям, возникает второй этап вызовов классификатора. Например, если "details=all" или "details" включают "celebrities", мы будем вызывать модели знаменитостей после вызова классификатора 86 категории, а результат будет включать категории пользователя. Это увеличит задержку для пользователей, заинтересованных в знаменитостях, по сравнению с параметром 1.

Все параметры запроса версии 1 в этом случае будут функционировать так же.  Если visualFeatures = категорий не указан, он явно включен.

### <a name="Step3">Шаг 3. Получение и интерпретация выходных данных JSON для analyze&visualFeatures=Tags, Description</a>

Ниже приведен пример:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Поле   | type  | Содержимое
------|------|------|
Теги    | object    | Объект верхнего уровня для массива тегов
tags[].Name | строка    | Ключевое слово тегов классификатора
tags[].Score    | number    | Оценка достоверности находится в пределах от 0 до 1.
description  | object   | Объект верхнего уровня для описаний.
description.tags[] |    строка  | Список тегов.  Если нет уверенности в способности создать заголовок, теги могут быть единственной информацией, доступной для вызывающего объекта.
description.captions[].text | строка    | Фраза, описывающая образ.
description.captions[].confidence   | number    | Достоверность для фразы.

### <a name="Step4">Шаг 4. Получение и интерпретация выходных данных JSON из моделей, предназначенных для определенных сфер</a>

**Вариант 1.** Ограниченный анализ — анализ только конкретной модели.

Массив тегов будет выходными данными, пример можно увидеть ниже.
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Вариант 2.** Расширенный анализ — анализ для предоставления дополнительных сведений на основе таксономии с 86 категориями.

Для моделей, предназначенных для определенных сфер, используется параметр 2 (расширенный анализ), возвращаемый тип категории — расширяется. См. пример ниже.
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

Поле категории — список одного или нескольких [86 категорий](../Category-Taxonomy.md) исходной таксономии. Обратите внимание, что категории, заканчивающиеся на символ подчеркивания, будут соответствовать этой категории и ее дочерним элементам (например, people_ как и people_group для модели знаменитостей).

Поле   | type  | Содержимое
------|------|------|
Категории | object | Объект верхнего уровня
categories[].name    | строка   | Имя 86 категорий таксономии
categories[].score  | number    | Оценка достоверности находится в пределах от 0 до 1.
categories[].detail  | object?      | Дополнительные сведения об объекте

Обратите внимание, что если несколько категорий совпадают (например, классификатор 86 категории возвращает оценку для people_ и people_young, когда модель = знаменитости), сведения присоединяются к совпадению наиболее общего уровня (в этом примере people_).

### <a name="Errors">Ответы на ошибки</a>
Они идентичны vision.analyze с дополнительной ошибкой NotSupportedModel (HTTP 400), которая может быть возвращена как в сценариях параметра 1, так и в параметре 2. Для параметра 2 (расширенный анализ), если любая из моделей, указанная в сведениях, не распознана, API вернет NotSupportedModel, даже если один или несколько из них являются допустимыми.  Пользователи могут вызывать listModels, чтобы узнать, какие модели поддерживаются.

### <a name="Summary">Сводка</a>

Основные функции API компьютерного зрения — это то, как можно загружать изображения и получать ценные метаданные взамен.

Чтобы использовать REST API, см. [Справочник по API компьютерного зрения компьютера](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
