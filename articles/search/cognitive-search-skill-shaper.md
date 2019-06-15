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
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540831"
---
#   <a name="shaper-cognitive-skill"></a>Когнитивный навык формирователя

**Shaper** навыков объединяет несколько входов в [сложный тип](search-howto-complex-data-types.md) , можно ссылаться в дальнейшем в конвейере обогащение. Навык **формирователя** фактически позволяет создать структуру, определить имя ее членов и присвоить значения каждому из них. Объединенные поля, полезно в сценариях поиска примеры объединение имени и фамилии в единую структуру, Город и состояния в единую структуру, или имя и Дата рождения в единой структуре для определения уникального идентификатора.

Версия API определяет глубину формирование вы можно добиться. 

| Версия API | Формирование поведения | 
|-------------|-------------------|
| Версии 2019-05-06-preview REST API (пакет SDK для .NET не поддерживается) | Сложные объекты несколько уровней, в одном **Shaper** определение навыков. |
| 2019-05-06 ** (общедоступная), 2017-11-11-Preview| Сложные объекты, на один уровень глубже. Многоуровневые фигуры требуется несколько шагов shaper цепочки.|

Предоставленный `api-version=2019-05-06-Preview`, **Shaper** показано навыков в [сценарий 3](#nested-complex-types) добавляет новый необязательный *sourcecontext объекта Elementgroupprototype* свойство к входным данным. *Источника* и *sourcecontext объекта Elementgroupprototype* свойства являются взаимоисключающими. Если входное значение в контексте навык, просто используйте *источника*. Если входные данные в *различных* контекста, чем контекст навыков использования *sourcecontext объекта Elementgroupprototype*. *Sourcecontext объекта Elementgroupprototype* необходимо определить Вход вложенной с конкретного элемента, к которому выполняется обращение как источник. 

В ответе, для всех версий API, имя выходных данных — это всегда «выходные данные». На внутреннем уровне конвейер можно сопоставить другое имя, например «analyzedText», как показано в примерах ниже, но **Shaper** навык, сам возвращает «выход» в ответе. Это может оказаться важным при обнаружении несоответствия имен во время отладки обогащенных документов или при создании пользовательского навыка с самостоятельным структурированием отклика.

> [!NOTE]
> **Shaper** навыков не привязан к API Cognitive Services и за ее использование плата не взимается. Но вам следует по-прежнему [подключать ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md), чтобы переопределить **бесплатную** категорию ресурса, ограниченную малым количеством обогащений в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Сценарий 1: сложные типы

Рассмотрим сценарий, где требуется создать структуру *analyzedText* с двумя членами: *text* и *sentiment*, соответственно. В индекс службы поиска Azure, называется полем для поиска нескольких частей *сложного типа* и часто создаются, когда исходные данные имеют соответствующие сложной структуре, которая сопоставляется с его.

Тем не менее, является другой подход для создания сложных типов через **Shaper** навыков. Включая этот навык в набор навыков, операций в памяти во время обработки набора навыков можно выводить данные фигуры с вложенными структурами, которые затем можно сопоставить со сложным типом, в индексе. 

В следующем примере определения навыков содержащий имена членов как входные данные. 


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

### <a name="sample-index"></a>Пример индекса

Набор навыков вызывается индексатор и индексатор, который требуется индекс. Представление сложного поля в индексе может выглядеть как в следующем примере. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Входные данные квалификации

Входящий документ JSON, вводя данных можно использовать для этого **Shaper** навыков может быть:

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


### <a name="skill-output"></a>Выходные данные квалификации

Навык **формирователя** создает элемент *analyzedText* с объединенными элементами *text* и *sentiment*. Эти выходные данные соответствуют схеме индекса. Будут импортированы и проиндексированы в индекс службы поиска Azure.

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

## <a name="scenario-2-input-consolidation"></a>Сценарий 2: Консолидация входной

В другом примере предположим, что на различных этапах обработки конвейера вы извлекли название книги и заголовки глав на разных страницах книги. Теперь вы можете создать единую структуру, состоящую из этих разнородных входных данных.

**Shaper** определение навыков для этого сценария может выглядеть как в следующем примере:

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
            "source": "/document/content/pages/*/chapterTitles/*/title"
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

### <a name="skill-output"></a>Выходные данные квалификации
В этом случае **Shaper** выполняет сведение всех глав, чтобы создать единый массив. 

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

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Сценарий 3: входной консолидации из вложенных контекстов

> [!NOTE]
> Вложенные структуры, поддерживаемые в [2019 г. версия REST API-05-06-Preview](search-api-preview.md) может использоваться в [хранилища знаний](knowledge-store-concept-intro.md) или в индексе поиска Azure.

Представьте себе, title, главы и содержимое книги и запустил сущности фраз распознавания и ключ на содержимое, а теперь требуется Агрегатная обработка результатов с различными умениями в одну форму с название главы, сущности и ключевые фразы.

**Shaper** определение навыков для этого сценария может выглядеть как в следующем примере:

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
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
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

### <a name="skill-output"></a>Выходные данные квалификации
В этом случае **Shaper** создает сложного типа. Эта структура существует в памяти. Если вы хотите сохранить его в хранилище базы знаний, следует создать проекцию в имеющийся набор навыков, который определяет характеристики хранилища.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
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
+ [Как использовать сложные типы](search-howto-complex-data-types.md)
+ [Обзор хранилища знаний](knowledge-store-concept-intro.md)
+ [Как приступить к работе с хранилищем базы знаний](knowledge-store-howto.md)