---
title: Когнитивный навык распознавания именованных сущностей
titleSuffix: Azure Cognitive Search
description: Извлеките именованные сущности для пользователя, расположения и организации из текста в конвейере обогащения искусственного интеллекта в Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791938"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Когнитивный навык распознавания именованных сущностей

Навык **Распознавание именованных сущностей** извлекает именованные сущности из текста. Доступные сущности включают в себя типы `person`, `location` и `organization`.

> [!IMPORTANT]
> Теперь уровень навыка распознавания сущностей больше не будет заменен на [Microsoft. Skills. Text. ентитирекогнитионскилл](cognitive-search-skill-entity-recognition.md). Поддержка остановлена 15 февраля 2019, а API был удален из продукта с 2 мая 2019 г. Следуйте рекомендациям в [нерекомендуемых навыках поиска](cognitive-search-skill-deprecated.md) , чтобы перейти на поддерживаемый навык.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, измеряемый [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Если вам нужно разбить данные перед отправкой для извлечения ключевой фразы, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | Описание |
|--------------------|-------------|
| Категории    | Массив категорий, который следует извлекать.  Возможные типы категорий: `"Person"`, `"Location"`, `"Organization"`. Если категория не указана, возвращаются все типы.|
|defaultLanguageCode |  Код языка вводимого текста. Поддерживается следующие языки: `de, en, es, fr, it`.|
| minimumPrecision  | Число от 0 до 1. Если точность меньше этого значения, сущность не возвращается. Значение по умолчанию — 0.|

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | Описание                   |
|---------------|-------------------------------|
| languageCode  | Необязательный элемент. Значение по умолчанию — `"en"`.  |
| текст          | Текст для анализа.          |

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода     | Описание                   |
|---------------|-------------------------------|
| Люди      | Массив строк, где каждая строка представляет имя человека. |
| Расположения  | Массив строк, где каждая строка представляет расположение. |
| organizations  | Массив строк, где каждая строка представляет организацию. |
| Сущности | Массив сложных типов. Каждый сложный тип включает следующие поля: <ul><li>категория (`"person"`, `"organization"` или `"location"`);</li> <li>значение (фактическое имя объекта);</li><li>смещение (расположение, где оно было найдено в тексте);</li><li>достоверность (значение от 0 до 1, которое представляет достоверность того, что значение является фактическим объектом).</li></ul> |

##  <a name="sample-definition"></a>Пример определения

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
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
+ [How to define a skillset](cognitive-search-defining-skillset.md) (Определение набора навыков)
+ [Навык распознавания сущностей](cognitive-search-skill-entity-recognition.md)
