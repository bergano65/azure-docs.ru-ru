---
title: 'Пример: вызов API анализа изображений. API компьютерного зрения'
titlesuffix: Azure Cognitive Services
description: Узнайте, как вызывать API компьютерного зрения с помощью REST в Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0e2767660edf2a9dbcb8617b07a6b9f71fedb743
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011250"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Пример: вызов API компьютерного зрения

В этом руководстве показано, как вызывать API компьютерного зрения с помощью REST. Примеры написаны как на языке C# с помощью клиентской библиотеки API компьютерного зрения, так и виде вызовов HTTP POST или GET. Мы сосредоточимся на следующем.

- Получение "тегов", "описания" и "категорий".
- Получение "информации об определенной области" (знаменитости).

## <a name="prerequisites"></a>Предварительные требования

- URL-адрес или путь к локально сохраненному изображению.
- Поддерживаемые методы ввода: двоичный файл изображения в формате RAW в форме приложения или потока октетов или URL-адреса изображения.
- Поддерживаемые форматы изображения: JPEG, PNG, GIF, BMP.
- Размер файла изображения: меньше 4 МБ.
- Размер изображения: больше 50 x 50 пикселей.
  
В приведенных ниже примерах демонстрируются следующие возможности.

1. Анализ изображения и получение массива возвращаемых тегов и описания.
2. Анализ изображения с помощью модели предметной области (в частности, модель "знаменитости") и получение соответствующего результата в перенастраиваемом JSON.

Функции разбиваются на:

- **Вариант 1.** Ограниченный анализ — анализ только конкретной модели.
- **Вариант 2.** Расширенный анализ — анализ для предоставления дополнительных сведений на основе [таксономии с 86 категориями](../Category-Taxonomy.md).
  
## <a name="authorize-the-api-call"></a>Авторизация вызова API

Каждый вызов API компьютерного зрения требует ключ подписки. Этот ключ должен быть либо передан через параметр строки запроса, либо указан в заголовке запроса.

На странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) можно получить бесплатную пробную версию ключа. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.

1. В качестве примера API компьютерного зрения см. приведенную ниже передачу ключа подписки через параметр строки запроса.

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Ключ подписки также можно указать в заголовке запроса HTTP.

```ocp-apim-subscription-key: <Your subscription key>```

1. При использовании клиентской библиотеки ключ подписки передается через конструктор класса VisionServiceClient.

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Передача изображения в службу API компьютерного зрения и получение тегов, описаний и знаменитостей

Простой способ выполнения вызовов API компьютерного зрения — передача изображения напрямую. Для этого нужно отправить запрос "POST" с типом содержимого потока приложений или октетов вместе с данными, считываемыми с изображения. Для "тегов" и "описания" этот метод отправки будет одинаковым для всех вызовов API компьютерного зрения. Единственным отличием будут параметры запроса пользователя. 

Вот как получить "теги" и "описание" для данного изображения.

**Вариант 1.** Получение списка тегов (Tags) и одного описания (Description)

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
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

###### <a name="tags-only"></a>Только список тегов

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Только список описаний

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Анализ содержимого, связанного с определенными предметными областями (знаменитости)

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

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Получение и интерпретация выходных данных JSON для анализа

Ниже приведен пример:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Поле | type | Содержимое
------|------|------|
Теги  | `object` | Объект верхнего уровня для массива тегов
tags[].Name | `string`  | Ключевое слово тегов классификатора
tags[].Score    | `number`  | Оценка достоверности находится в пределах от 0 до 1.
description  | `object` | Объект верхнего уровня для описаний.
description.tags[] |    `string`    | Список тегов.  Если нет уверенности в способности создать заголовок, теги могут быть единственной информацией, доступной для вызывающего объекта.
description.captions[].text | `string`  | Фраза, описывающая образ.
description.captions[].confidence   | `number`  | Достоверность для фразы.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Получение и интерпретация выходных данных JSON из моделей, предназначенных для определенных сфер

**Вариант 1.** Ограниченный анализ — анализ только конкретной модели.

Массив тегов будет выходными данными, пример можно увидеть ниже.

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**Вариант 2.** Расширенный анализ — анализ для предоставления дополнительных сведений на основе таксономии с 86 категориями.

Для моделей, предназначенных для определенных сфер, используется параметр 2 (расширенный анализ), возвращаемый тип категории — расширяется. См. пример ниже.

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Поле категории — список одного или нескольких [86 категорий](../Category-Taxonomy.md) исходной таксономии. Обратите внимание, что категории, заканчивающиеся на символ подчеркивания, будут соответствовать этой категории и ее дочерним элементам (например, people_ как и people_group для модели знаменитостей).

Поле   | type  | Содержимое
------|------|------|
Категории | `object`   | Объект верхнего уровня
categories[].name    | `string` | Имя 86 категорий таксономии
categories[].score  | `number`  | Оценка достоверности находится в пределах от 0 до 1.
categories[].detail  | `object?`      | Дополнительные сведения об объекте

Обратите внимание, что если несколько категорий совпадают (например, классификатор 86 категории возвращает оценку для people_ и people_young, когда модель = знаменитости), сведения присоединяются к совпадению наиболее общего уровня (в этом примере people_).

## <a name="errors-responses"></a>Ответы на ошибки

Они идентичны vision.analyze с дополнительной ошибкой NotSupportedModel (HTTP 400), которая может быть возвращена как в сценариях параметра 1, так и в параметре 2. Для параметра 2 (расширенный анализ), если любая из моделей, указанная в сведениях, не распознана, API вернет NotSupportedModel, даже если один или несколько из них являются допустимыми.  Пользователи могут вызывать listModels, чтобы узнать, какие модели поддерживаются.

## <a name="next-steps"></a>Дополнительная информация

Чтобы использовать REST API, см. [Справочник по API компьютерного зрения компьютера](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
