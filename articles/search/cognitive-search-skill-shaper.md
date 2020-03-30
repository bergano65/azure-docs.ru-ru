---
title: Когнитивный навык формирователя
titleSuffix: Azure Cognitive Search
description: Извлекайте метаданные и структурированную информацию из неструктурированных данных и формируйте их как сложный тип в конвейере обогащения ИИ в Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754116"
---
# <a name="shaper-cognitive-skill"></a>Когнитивный навык формирователя

Навык **Shaper** объединяет несколько входов в [сложный тип,](search-howto-complex-data-types.md) на который можно ссылаться позже в конвейере обогащения. Навык **Shaper** позволяет по существу создать структуру, определить имя членов этой структуры и назначить значения каждому члену. Примеры консолидированных полей, полезных в поисковых сценариях, включают объединение имени и фамилии в единую структуру, город и государство в единую структуру, или имя и дату рождения в единую структуру для создания уникальной идентичности.

Кроме того, навык **Shaper,** иллюстрированный в [сценарии 3,](#nested-complex-types) добавляет дополнительное свойство *sourceContext* к входной связи. *Свойства источника* и *источникаКонтекст* являются взаимоисключающими. Если вход находится в контексте навыка, просто используйте *источник.* Если ввод находится в *ином* контексте, чем контекст навыка, используйте *sourceContext.* *SourceContext* требует определить вложенный вход с конкретным элементом, который рассматривается в качестве источника. 

Имя вывода всегда "выход". Внутренне конвейер может отображать другое имя, например ,analyzedText», как показано на примерах ниже, но сам навык **Shaper** возвращает "выход" в ответ. Это может оказаться важным при обнаружении несоответствия имен во время отладки обогащенных документов или при создании пользовательского навыка с самостоятельным структурированием отклика.

> [!NOTE]
> Навык **Shaper** не привязан к API когнитивных услуг, и с вас не взимается плата за его использование. Но вам следует по-прежнему [подключать ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md), чтобы переопределить **бесплатную** категорию ресурса, ограниченную малым количеством обогащений в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Сценарий 1: сложные типы

Рассмотрим сценарий, где требуется создать структуру *analyzedText* с двумя членами: *text* и *sentiment*, соответственно. В индексе многокомпонентное поле поиска называется *сложным типом,* и оно часто создается, когда исходные данные имеют соответствующую сложную структуру, которая отображает его.

Однако другой подход к созданию сложных типов — это навык **Shaper.** Включив этот навык в набор навыков, операции в памяти во время обработки навыков могут выводить формы данных с вложенными структурами, которые затем могут быть отображены в сложный тип в индексе. 

В следующем примере определение навыка предоставляет имена членов в качестве ввода. 


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

### <a name="sample-index"></a>Индекс образца

Набор навыков вызывается индексом, а индексатортребует индекс. Сложное представление поля в индексе может выглядеть следующим примером. 

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

### <a name="skill-input"></a>Ввод навыков

Входящий документ JSON, предоставляющий пригодный для ввода для этого **навыка Shaper,** может быть:

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


### <a name="skill-output"></a>Выход навыка

Навык **Shaper** генерирует новый элемент под названием *analyzedText* с комбинированными элементами *текста* и *настроения.* Этот вывод соответствует схеме индекса. Он будет импортирован и проиндексирован в индекс когнитивного поиска Azure.

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

## <a name="scenario-2-input-consolidation"></a>Сценарий 2: консолидация ввода

В другом примере предположим, что на различных этапах обработки конвейера вы извлекли название книги и заголовки глав на разных страницах книги. Теперь вы можете создать единую структуру, состоящую из этих разнородных входных данных.

Определение навыка **Shaper** для этого сценария может выглядеть следующим примером:

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

### <a name="skill-output"></a>Выход навыка
В этом случае **Shaper** выравнивает все заголовки глав для создания единого массива. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Сценарий 3: консолидация ввода из вложенных контекстов

Представьте, что у вас есть название, главы и содержимое книги, и вы полнить распознавание сущности и ключевые фразы по содержанию и теперь необходимо агрегировать результаты различных навыков в одну форму с названием главы, сущностями и ключевыми фразами.

Определение навыка **Shaper** для этого сценария может выглядеть следующим примером:

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

### <a name="skill-output"></a>Выход навыка
В этом случае **Shaper** создает сложный тип. Эта структура существует в памяти. Если вы хотите сохранить его в [хранилище знаний,](knowledge-store-concept-intro.md)вы должны создать проекцию в наборе навыков, которая определяет характеристики хранения.

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

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Как использовать сложные типы](search-howto-complex-data-types.md)
+ [Что собой представляет хранилище знаний в службе "Поиск Azure"](knowledge-store-concept-intro.md)
+ [Создание хранилища знаний в REST](knowledge-store-create-rest.md)