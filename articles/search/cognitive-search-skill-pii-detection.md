---
title: Когнитивные навыки PII Detection (предварительный просмотр)
titleSuffix: Azure Cognitive Search
description: Извлеките и маскируйте личную информацию из текста в конвейере обогащения в Azure Cognitive Search. Этот навык в настоящее время в публичном предварительном просмотре.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298879"
---
#    <a name="pii-detection-cognitive-skill"></a>Когнитивные навыки PII Detection

> [!IMPORTANT] 
> Этот навык в настоящее время в публичном предварительном просмотре. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). В настоящее время нет портала или поддержки .NET SDK.

Навык **обнаружения PII** извлекает личную информацию из вхотворного текста и дает вам возможность замаскировать ее из этого текста различными способами. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

> [!NOTE]
> По мере расширения сферы охвата за счет увеличения частоты обработки, добавления дополнительных документов или добавления большего числа алгоритмов ИИ необходимо [прикрепить ресурс Cognitive Services.](cognitive-search-attach-cognitive-services.md) Плата взимается при вызове API в Cognitive Services и извлечении изображений при распознавании документов в службе "Когнитивный поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на службу "Когнитивный поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен соразмеровать 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)символов. Если вам нужно разбить данные перед отправкой на навык, рассмотрите возможность использования [навыка Text Split.](cognitive-search-skill-textsplit.md)

## <a name="skill-parameters"></a>Параметры навыков

Параметры чувствительны к делу, и все они не являются обязательными.

| Имя параметра     | Описание |
|--------------------|-------------|
| defaultLanguageCode |    Код языка вводимого текста. Пока только `en` поддерживается. |
| minimumPrecision | Значение от 0,0 до 1,0. Если оценка достоверности `piiEntities` (на выходе) ниже `minimumPrecision` заданного значения, объект не возвращается и не маскируется. Значение по умолчанию — 0,0. |
| маскировкаРежим | Параметр, который предоставляет различные способы маскировки обнаруженного PII в тексте ввода. Поддерживаются следующие варианты. <ul><li>`none`(по умолчанию): Это означает, что `maskedText` маскировка не будет выполнена и выход не будет возвращен. </li><li> `redact`: Эта опция удалит обнаруженные сущности из вхотворного текста и не заменит их ничем. Обратите внимание, что в этом `piiEntities` случае смещение в выходе будет по отношению к исходутичку текста, а не замаскированный текст. </li><li> `replace`: Этот параметр заменит обнаруженные сущности `maskingCharacter` символом, приведенным в параметре.  Символ будет повторяться по длине обнаруженной сущности, так что смещения будут правильно соответствовать `maskedText`как тексту ввода, так и выходу.</li></ul> |
| маскировкаХарактер | Символ, который будет использоваться для маски `maskingMode` текста, `replace`если параметр установлен на . Поддерживаются следующие `*` варианты: `#` `X`(по умолчанию), . Этот параметр `null` может `maskingMode` быть только `replace`в том случае, если не установлен на . |


## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | Описание                   |
|---------------|-------------------------------|
| languageCode    | Необязательный параметр. Значение по умолчанию — `en`.  |
| text          | Текст для анализа.          |

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода      | Описание                   |
|---------------|-------------------------------|
| piiEntities | Массив сложных типов, содержащий следующие поля: <ul><li>текст (фактический PII как извлеченный)</li> <li>type</li><li>Подтип</li><li>оценка (более высокая стоимость означает, что это, скорее всего, будет реальной сущности)</li><li>смещение (в текст ввода)</li><li>length</li></ul> </br> [Возможные типы и подтипы можно найти здесь.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| маскиТекст | Если `maskingMode` он установлен на `none`значение, кроме, этот вывод будет строковым результатом маскировки, выполненной на вхотворном тексте, описанном выбранным. `maskingMode`  Если `maskingMode` `none`установлен, этот выход не будет присутствовать. |

##    <a name="sample-definition"></a>Пример определения

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Пример ввода

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Пример выходных данных

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Обратите внимание, что смещения, возвращенные для сущностей в выходе этого навыка, непосредственно возвращаются из [API Text Analytics,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)что означает, что если вы используете их для индексирования в исходную строку, вы должны использовать класс [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) в .NET для извлечения правильного содержимого.  [Более подробную информацию можно найти здесь.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Случаи ошибки и предупреждения
Если языковой код документа не поддерживается, предупреждение возвращается и не извлекаются сущности.
Если текст пуст, появится предупреждение.
Если текст больше 50 000 знаков, будут проанализированы только первые 50 000 знаков и появится предупреждение.

Если навык возвращает предупреждение, `maskedText` вывод может быть пустым.  Это означает, что если вы ожидаете, что выход будет существовать для ввода в более поздние навыки, он не будет работать, как предполагалось. Имейте это в виду при написании вашего определения skillset.

## <a name="see-also"></a>См. также

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
