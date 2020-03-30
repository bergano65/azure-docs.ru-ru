---
title: Когнитивный навык тональности
titleSuffix: Azure Cognitive Search
description: Извлеките положительно-отрицательную оценку настроений из текста в конвейере обогащения ИИ в Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791910"
---
# <a name="sentiment-cognitive-skill"></a>Когнитивный навык тональности

Навык **тональности** обеспечивает полный спектр оценки положительной и отрицательной тональности в неструктурированном тексте и возвращает для каждой записи числовую оценку от 0 до 1. Оценки, близкие к 1, указывают на позитивную тональность, а оценки, близкие к 0, — на негативную. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

> [!NOTE]
> По мере расширения сферы охвата за счет увеличения частоты обработки, добавления дополнительных документов или добавления большего числа алгоритмов ИИ необходимо [прикрепить ресурс Cognitive Services.](cognitive-search-attach-cognitive-services.md) Плата взимается при вызове API в Cognitive Services и извлечении изображений при распознавании документов в службе "Когнитивный поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на службу "Когнитивный поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен соразмеровать 5000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)символов. Если вам нужно разбить данные перед отправкой в анализатор тональности, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра |                      |
|----------------|----------------------|
| defaultLanguageCode | (Необязательно.) Код языка применяется к документам, в которых не указан язык явным образом. <br/> Посмотреть [полный список поддерживаемых языков](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Входные данные навыков 

| Ввод имени | Описание |
|--------------------|-------------|
| text | Анализируемый текст.|
| languageCode  |  (Дополнительно.) Строка, указывающая язык записей. Если этот параметр не задан, используется значение по умолчанию "en". <br/>Ознакомьтесь с [полным списком поддерживаемых языков](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода | Описание |
|--------------------|-------------|
| score | Значение от 0 до 1, которое представляет тональность анализируемого текста. Значения, близкие к 0, имеют отрицательную тональность, близкие к 0,5 имеют нейтральную, а значения, близкие к 1, имеют положительную тональность.|


##  <a name="sample-definition"></a>Пример определения

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
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
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Примечания
Если не указано, оценка тональности не возвращается для этих записей.

## <a name="error-cases"></a>Варианты ошибок
Если язык не поддерживается, создается ошибка и оценка не возвращается.

## <a name="see-also"></a>См. также

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
