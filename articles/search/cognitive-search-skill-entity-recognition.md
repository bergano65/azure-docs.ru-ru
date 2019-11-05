---
title: Когнитивный навык распознавания сущностей
titleSuffix: Azure Cognitive Search
description: Извлечение различных типов сущностей из текста в конвейере обогащения в Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 559d8cb25624c1d8bebb2969fbeeb80bdcc020e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479747"
---
#   <a name="entity-recognition-cognitive-skill"></a>Когнитивный навык распознавания сущностей

Навык **распознавания сущностей** извлекает сущности различных типов из текста. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, измеряемый [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Если вам нужно разбить данные перед отправкой для извлечения ключевой фразы, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Параметры навыков

Все параметры чувствительны к регистру и являются необязательными.

| Имя параметра     | Description (Описание) |
|--------------------|-------------|
| Категории    | Массив категорий, который следует извлекать.  Возможные типы категорий: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Если категория не указана, возвращаются все типы.|
|defaultLanguageCode |  Код языка вводимого текста. Поддерживается следующие языки: `de, en, es, fr, it`.|
|minimumPrecision | Значение от 0 до 1. Если показатель достоверности (в `namedEntities` выходных данных) меньше этого значения, сущность не возвращается. Значение по умолчанию — 0. |
|includeTypelessEntities | Задайте значение `true`, если нужно распознавать хорошо известные сущности, не соответствующие текущим категориям. Распознанные сущности возвращаются в `entities` сложного выходного поля. Например, "Windows 10" является известной сущностью (продуктом), но так как "продукты" не являются поддерживаемой категорией, эта сущность будет включена в поле выходных данных сущностей. Значение по умолчанию — `false`. |


## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | Description (Описание)                   |
|---------------|-------------------------------|
| languageCode  | необязательный параметр. Значение по умолчанию — `"en"`.  |
| text          | Текст для анализа.          |

## <a name="skill-outputs"></a>Выходные данные навыка

> [!NOTE]
> Для некоторых языков категории сущностей не поддерживаются. Только языки _en_, _es_ поддерживают извлечение типов `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`.

| Имя вывода     | Description (Описание)                   |
|---------------|-------------------------------|
| Люди      | Массив строк, где каждая строка представляет имя человека. |
| locations  | Массив строк, где каждая строка представляет расположение. |
| organizations  | Массив строк, где каждая строка представляет организацию. |
| quantities  | Массив строк, где каждая строка представляет количество. |
| dateTimes  | Массив строк, где каждая строка представляет собой значение даты и времени (как оно отображается в тексте). |
| urls | Массив строк, где каждая строка представляет собой URL-адрес. |
| emails | Массив строк, где каждая строка представляет собой адрес электронной почты. |
| namedEntities | Массив сложных типов, содержащий следующие поля: <ul><li>category</li> <li>значение (фактическое имя сущности)</li><li>смещение (расположение, где оно было найдено в тексте);</li><li>достоверность (более высокое значение означает, что она больше, чем фактическая сущность)</li></ul> |
| Сущности | Массив сложных типов, содержащий подробные сведения о сущностях, извлеченных из текста, со следующими полями. <ul><li> name (Фактическое имя сущности. Представляет собой "нормализованную" форму);</li><li> wikipediaId;</li><li>wikipediaLanguage;</li><li>wikipediaUrl (ссылка на страницу Википедии для сущности);</li><li>bingId</li><li>type (категория распознанной сущности);</li><li>subType (доступно только для определенных категорий, что дает дополнительное представление о типе сущности);</li><li> matches (имеющаяся составная коллекция);<ul><li>text (необработанный текст для сущности);</li><li>offset (расположение, где было найдено смещение);</li><li>length (длина необработанного текста сущности).</li></ul></li></ul> |

##  <a name="sample-definition"></a>Пример определения

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Пример ввода

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Пример выходных данных

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Варианты ошибок
Если код языка для документа не поддерживается, возвращается ошибка и сущности не извлекаются.

## <a name="see-also"></a>Дополнительные материалы

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
