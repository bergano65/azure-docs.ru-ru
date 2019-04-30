---
title: Навык когнитивного поиска формирователя (служба "Поиск Azure")
description: Извлечение метаданных и структурированных сведений из неструктурированных данных и их формирование в виде сложного типа в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c55783e9b209a1280a21edca34b75e72481f4cb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127062"
---
#   <a name="shaper-cognitive-skill"></a>Когнитивный навык формирователя

**Shaper** навыков объединяет несколько входов в сложный тип, который можно ссылаться в дальнейшем в конвейере обогащение. Навык **формирователя** фактически позволяет создать структуру, определить имя ее членов и присвоить значения каждому из них. Объединенные поля, полезно в сценариях поиска примеры объединение имени и фамилии в единую структуру, Город и состояния в единую структуру, или имя и Дата рождения в единой структуре для определения уникального идентификатора.

По умолчанию этот метод поддерживает объекты с глубиной в один уровень. Для более сложных объектов можно соединить в цепочку несколько шагов **формирователя**.

В отклике выходные данные всегда имеют имя "output". На внутреннем уровне конвейер может сопоставить с "output" другое имя, например "analyzedText", как в указанных ниже примерах, однако сам навык **формирователя** возвращает "output" в ответ. Это может оказаться важным при обнаружении несоответствия имен во время отладки обогащенных документов или при создании пользовательского навыка с самостоятельным структурированием отклика.

> [!NOTE]
> Этот навык не привязан к API Cognitive Services, и за его использование плата не взимается. Но вам следует по-прежнему [подключать ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md), чтобы переопределить **бесплатную** категорию ресурса, ограниченную малым количеством обогащений в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Пример 1: сложные типы

Рассмотрим сценарий, где требуется создать структуру *analyzedText* с двумя членами: *text* и *sentiment*, соответственно. Поддерживающее поиск составное поле в службе "Поиск Azure" называется *сложным типом* и пока не поддерживается без дополнительной настройки. В этой предварительной версии навык **формирователя** можно использовать для создания полей сложного типа в индексе. 

Приведенный ниже пример предоставляет имена членов в качестве входных данных. Структура выходных данных (составное поле в Поиске Azure) указано с помощью *targetName*. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Пример ввода
Документ JSON, предоставляющий полезные входные данные для этого навыка **формирователя**, может иметь следующий вид:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>Пример выходных данных
Навык **формирователя** создает элемент *analyzedText* с объединенными элементами *text* и *sentiment*. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>Пример 2: консолидация входных данных

В другом примере предположим, что на различных этапах обработки конвейера вы извлекли название книги и заголовки глав на разных страницах книги. Теперь вы можете создать единую структуру, состоящую из этих разнородных входных данных.

Определение навыка формирователя для этого сценария может выглядеть, как в следующем примере:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="sample-output"></a>Пример выходных данных
В этом случае формирователь преобразует все заголовки глав в плоскую структуру, чтобы создать один массив. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)

