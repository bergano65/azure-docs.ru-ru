---
title: Навык когнитивного поиска тональности — Поиск Azure
description: Извлечение позитивно-негативной оценки тональности из текста в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: d4ac762f94f05d165eb157f4cce183055f5fe099
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840983"
---
#   <a name="sentiment-cognitive-skill"></a>Когнитивный навык тональности

Навык **тональности** обеспечивает полный спектр оценки положительной и отрицательной тональности в неструктурированном тексте и возвращает для каждой записи числовую оценку от 0 до 1. Оценки, близкие к 1, указывают на позитивную тональность, а оценки, близкие к 0, — на негативную. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Плата взимается при вызове API в Cognitive Services и извлечении изображений при открытии документов в службе "Поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 5000 символов, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)измеряемый. Если вам нужно разбить данные перед отправкой в анализатор тональности, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра |                      |
|----------------|----------------------|
| defaultLanguageCode | (Необязательно.) Код языка применяется к документам, в которых не указан язык явным образом. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](../cognitive-services/text-analytics/text-analytics-supported-languages.md). |

## <a name="skill-inputs"></a>Входные данные навыков 

| Ввод имени | Описание |
|--------------------|-------------|
| text | Анализируемый текст.|
| languageCode  |  (Дополнительно.) Строка, указывающая язык записей. Если этот параметр не задан, используется значение по умолчанию "en". <br/>Ознакомьтесь с [полным списком поддерживаемых языков](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода | Описание |
|--------------------|-------------|
| приложения | Значение от 0 до 1, которое представляет тональность анализируемого текста. Значения, близкие к 0, имеют отрицательную тональность, близкие к 0,5 имеют нейтральную, а значения, близкие к 1, имеют положительную тональность.|


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

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
