---
title: Текстовый перевод когнитивных навыков
titleSuffix: Azure Cognitive Search
description: Оценивает текст и для каждой записи возвращает текст, переведенный на указанный целевой язык в конвейере обогащения ИИ в Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460770"
---
#   <a name="text-translation-cognitive-skill"></a>Текстовый перевод когнитивных навыков

Навык **перевода текста** оценивает текст и для каждой записи возвращает текст, переведенный на указанный целевой язык. Этот навык использует [API Текста переводчика v3.0, доступный](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) в когнитивных службах.

Эта возможность полезна, если вы ожидаете, что документы не могут быть на одном языке, и в этом случае вы можете нормализовать текст на одном языке, прежде чем индексировать для поиска путем его перевода.  Это также полезно для случаев использования локализации, где вы можете иметь копии одного и того же текста, доступные на нескольких языках.

[API-aPI Translator v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) — это нерегиональная когнитивная служба, что означает, что ваши данные не гарантированно останутся в том же регионе, что и ваш ресурс Azure Cognitive Search или прилагаемый ресурс Cognitive Services.

> [!NOTE]
> По мере расширения сферы охвата за счет увеличения частоты обработки, добавления дополнительных документов или добавления большего числа алгоритмов ИИ необходимо [прикрепить ресурс Cognitive Services.](cognitive-search-attach-cognitive-services.md) Плата взимается при вызове API в Cognitive Services и извлечении изображений при распознавании документов в службе "Когнитивный поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на службу "Когнитивный поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен соразмеровать 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)символов. Если вам нужно разбить данные перед отправкой на навык исправный перевод текста, рассмотрите возможность использования [навыка Text Split.](cognitive-search-skill-textsplit.md)

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Входные данные                | Описание |
|---------------------|-------------|
| defaultToLanguageCode | (Обязательно) Языковой код для перевода документов в документы, в которых не указаны язык прямо. <br/> Смотрите [полный список поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | (Необязательно) Языковой код для перевода документов из документов, которые не указывают из языка явно.  Если не указано значение по умолчанию, то для определения языка будет использоваться автоматическое обнаружение языка, предоставляемое API Translator Text. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | (Необязательно) Языковой код для перевода документов, когда не предусмотрено ни ввода LanguageCode, ни параметр по умолчаниюFromLanguageCode, а автоматическое обнаружение языка не работает.  Если предложенныйИз языка не указан, английский (en) будет использоваться в качестве предложенногоИз языка. <br/> Смотрите [полный список поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени     | Описание |
|--------------------|-------------|
| text | Текст, который будет переведен.|
| toLanguageCode    | Строка с указанием языка, на который должен быть переведен текст. Если этот ввод не указан, для перевода текста будет использоваться код defaultToLanguageCode. <br/>Посмотреть [полный список поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| отLanguageCode  | Строка с указанием текущего языка текста. Если этот параметр не указан, для перевода текста будет использован по умолчаниюFromLanguageCode (или автоматическое обнаружение языка, если не предусмотрен код по умолчанию) . <br/>Посмотреть [полный список поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода    | Описание |
|--------------------|-------------|
| TranslatedText | Строка результат перевода текста с translatedFromLanguageCode на translatedToLanguageCode.|
| переводtoLanguageCode  | Строка с указанием языкового кода, на который был переведен текст. Полезно, если вы переводите на несколько языков и хотите иметь возможность отслеживать, какой текст, какой язык.|
| переводFromLanguageCode    | Строка, указывающая на языковый код, с котором был переведен текст. Полезно, если вы выбрали вариант автоматического обнаружения языка, так как этот вывод даст вам результат этого обнаружения.|

##  <a name="sample-definition"></a>Пример определения

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>Пример выходных данных

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Ошибки и предупреждения
Если вы предоставляете неподдерживаемый языковой код для языка с или на язык, создается ошибка и текст не переводится.
Если текст пуст, появится предупреждение.
Если ваш текст больше 50 000 символов, будет переведено только первые 50 000 символов и выпущено предупреждение.

## <a name="see-also"></a>См. также

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
