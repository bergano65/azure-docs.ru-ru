---
title: Когнитивный навык распознавания языка
titleSuffix: Azure Cognitive Search
description: Вычисляет неструктурированный текст и для каждой записи возвращает идентификатор языка с показателем, указывающим стойкость анализа в конвейере обогащения искусственного интеллекта в Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ec9ea9cfbae314297c5b59f6a07bcebaef6a5c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791955"
---
#   <a name="language-detection-cognitive-skill"></a>Когнитивный навык распознавания языка

**Распознавание языка** навык определяет язык вводимого текста и сообщает один код языка для каждого документа, отправленного по запросу. Код языка сопряжен с оценкой, указывающей степень анализа. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

Эта возможность особенно полезна, когда требуется предоставить язык текста в качестве входных данных для других навыков (например, [навыка анализа тональности](cognitive-search-skill-sentiment.md) или [разделения текста](cognitive-search-skill-textsplit.md)).

Определение языка использует библиотеки обработки естественного языка Bing, что превышает число [поддерживаемых языков и регионов](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) , перечисленных для анализ текста. Точный список языков не публикуется, но включает в себя все широко знакомые языки, а также варианты, диалекты и некоторые региональные и культурные языки. Если содержимое представлено на менее часто используемом языке, можно [попробовать распознавание языка API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) , чтобы узнать, возвращает ли он код. Для языков, которые не удалась распознать, возвращается ответ `unknown`.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, измеряемый [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Если вам нужно разбить данные перед отправкой в тональный анализатор, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Входные данные навыков

Параметры зависят от регистра.

| Входные данные     | Описание |
|--------------------|-------------|
| текст | Анализируемый текст.|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода    | Описание |
|--------------------|-------------|
| languageCode | Код языка ISO 6391 для распознанного языка. Например, en. |
| LanguageName | Имя языка. Например, английский. |
| приложения | Значение от 0 до 1. Вероятность, что язык правильно распознан. Оценка может быть меньше 1, если в предложении используются разные языки.  |

##  <a name="sample-definition"></a>Пример определения

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Пример выходных данных

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Варианты ошибок
Если текст написан на неподдерживаемом языке, возникает ошибка и идентификатор языка не возвращается.

## <a name="see-also"></a>Дополнительные материалы

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [How to define a skillset](cognitive-search-defining-skillset.md) (Определение набора навыков)
