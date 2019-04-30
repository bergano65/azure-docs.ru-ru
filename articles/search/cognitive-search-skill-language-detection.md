---
title: 'Навык когнитивного поиска: распознавание языка (служба "Поиск Azure")'
description: Анализирует неструктурированный текст и для каждой записи возвращает идентификатор языка с оценкой, указывающей степень анализа в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341637"
---
#   <a name="language-detection-cognitive-skill"></a>Когнитивный навык распознавания языка

**Распознавание языка** навыков определяет язык ввода текста и сообщает код для одного языка для каждого документа, отправляемого в запросе. Код языка сопряжен с оценкой, указывающей степень анализа. Этот навык использует модели машинного обучения, предоставляемые функцией [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) в Cognitive Services.

Эта возможность особенно полезна, когда требуется предоставить язык текста в качестве входных данных для других навыков (например, [навыка анализа тональности](cognitive-search-skill-sentiment.md) или [разделения текста](cognitive-search-skill-textsplit.md)).

Распознавание языка использует Bing библиотеки обработки естественного языка, который превышает число из [поддерживаемых языков и регионов](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) для анализа текста. Точный список языков не публикуется, но включает в себя все языки широко произнести, а также варианты, диалекты и некоторых языков, региональных и культурных. Если имеется содержимое, выраженное как менее часто используемый язык, вы можете [попробуйте API обнаружения языка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) для просмотра, если он возвращает код. Ответ для языков, которые не удается обнаружить `unknown`.

> [!NOTE]
> Начиная с 21 декабря 2018 г. можно [связывать ресурсы Cognitive Services](cognitive-search-attach-cognitive-services.md) с набором навыков службы "Поиск Azure". Это позволяет нам взимать плату за выполнение набора навыков. С этого момента мы также начали начислять плату за извлечение изображений при открытии документов. Извлечение текста из документов будет выполняться бесплатно, как и прежде.
>
> За операции с применением [встроенных навыков](cognitive-search-predefined-skills.md) взимается [плата по мере использования по тарифам для служб Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services), как если бы вы выполнили эту задачу непосредственно. Плата за извлечение изображения взимается по тарифам службы "Поиск Azure", которая сейчас предлагается по цене предварительной версии. Дополнительные сведения см. на странице [Цены на Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400) и в разделе [Как работает выставление счетов](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи — 50 000 знаков, как определено в `String.Length`. Если вам нужно разбить данные перед отправкой в тональный анализатор, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Входные данные навыков

Параметры зависят от регистра.

| Входные данные     | Описание |
|--------------------|-------------|
| Text | Анализируемый текст.|

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

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
