---
title: 'Навык когнитивного поиска: распознавание языка (Поиск Azure) | Документация Майкрософт'
description: Анализирует неструктурированный текст и для каждой записи возвращает идентификатор языка с оценкой, указывающей степень анализа в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2fd1c1ec0d2442afd6367e1d35af6f798dced2c7
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733284"
---
#   <a name="language-detection-cognitive-skill"></a>Когнитивный навык распознавания языка

Навык **распознавания языка** определяет введенный текст на более чем 120 языках и сообщает код одного языка для каждого документа, отправленного по запросу. Код языка сопряжен с оценкой, указывающей степень анализа.

Эта возможность особенно полезна, когда требуется предоставить язык текста в качестве входных данных для других навыков (например, [навыка анализа тональности](cognitive-search-skill-sentiment.md) или [разделения текста](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> Служба "Когнитивный поиск" находится в общедоступной предварительной версии. Выполнение набора навыков, а также извлечение и нормализация изображений в настоящее время предлагаются бесплатно. Цена на эти функции будет объявлена позже. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи — 50 000 знаков, как определено в `String.Length`. Если вам нужно разбить данные перед отправкой в тональный анализатор, можно воспользоваться [навыком разделения текста](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Входные данные навыков

Параметры зависят от регистра.

| Входные данные     | ОПИСАНИЕ |
|--------------------|-------------|
| текст | Анализируемый текст.|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода    | ОПИСАНИЕ |
|--------------------|-------------|
| languageCode | Код языка ISO 6391 для распознанного языка. Например, en. |
| LanguageName | Имя языка. Например, английский. |
| приложения | Значение от 0 до 1. Вероятность, что язык правильно распознан. Оценка может быть меньше 1, если в предложении используются разные языки.  |

##  <a name="sample-definition"></a>Пример определения

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
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
