---
title: Когнитивный навык извлечения ключевой фразы
titleSuffix: Azure Cognitive Search
description: Вычисляет неструктурированный текст и для каждой записи возвращает список ключевых фраз в конвейере обогащения искусственного интеллекта в Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791977"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Когнитивный навык извлечения ключевой фразы

Навык **Извлечение ключевой фразы** оценивает неструктурированный текст и для каждой записи возвращает список ключевых фраз. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

Эта возможность полезна, если необходимо быстро определить основные тезисы в записи. Например, для данного входного текста "Еда была вкусной и были замечательные сотрудники", служба вернет "еда" и "замечательные сотрудники".

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, измеряемый [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Если вам нужно разбить данные перед отправкой для извлечения ключевой фразы, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Входные данные                | Описание |
|---------------------|-------------|
| defaultLanguageCode | (Необязательно.) Код языка применяется к документам, в которых не указан язык явным образом.  Если код языка по умолчанию не указан, английский (en) используется как язык по умолчанию. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Необязательно.) Максимальное количество ключевых фраз для создания. |

## <a name="skill-inputs"></a>Входные данные навыков

| Входные данные     | Описание |
|--------------------|-------------|
| текст | Анализируемый текст.|
| languageCode  |  Строка, указывающая язык записей. Если этот параметр не указан, для анализа записей будет использоваться код языка по умолчанию. <br/>Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).|

##  <a name="sample-definition"></a>Пример определения

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Ошибки и предупреждения
Если указать неподдерживаемый код языка, сформируется сообщение об ошибке, а ключевые фразы не будут извлечены.
Если текст пуст, появится предупреждение.
Если текст больше 50 000 знаков, будут проанализированы только первые 50 000 знаков и появится предупреждение.

## <a name="see-also"></a>Дополнительные материалы

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [How to define a skillset](cognitive-search-defining-skillset.md) (Определение набора навыков)
