---
title: Условные когнитивные навыки
titleSuffix: Azure Cognitive Search
description: Условный навык в Azure Cognitive Search позволяет фильтровать, создавать по умолчанию и объединять значения в определении набора навыков.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792048"
---
# <a name="conditional-cognitive-skill"></a>Условные когнитивные навыки

**Условный** навык позволяет сценариям когнитивного поиска Azure, требующим операции Boolean, определить данные для присвоения вывода. Эти сценарии включают фильтрацию, назначение значения по умолчанию и слияние данных на основе условия.

Следующий псевдокод демонстрирует, чего выполняет условный навык:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Этот навык не привязан к API когнитивных служб Azure, и с него не взимается плата. Тем не менее, вы все равно должны [прикрепить ресурс Cognitive Services,](cognitive-search-attach-cognitive-services.md) чтобы переопределить опцию ресурса "Свободный", которая ограничивает вас небольшим количеством обогащений в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Оцененные поля

Этот навык является особенным, потому что его входы оцениваются полями.

Следующие элементы являются действительными значениями выражения:

-   Пути аннотации (пути в выражениях должны быть разграничены "$(" и "))
 <br/>
    Примеры:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Буквы (строки, числа, истинные, ложные, нулевые) <br/>
    Примеры:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Выражения, которые используют операторы сравнения (я, ! , >, >, <, <) <br/>
    Примеры:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Выражения, которые используют Boolean операторов (&&, я, !, <br/>
    Примеры:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Выражения, которые используют числовые операторы (я, -, \*,, %) <br/>
    Примеры: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Поскольку условный навык поддерживает оценку, его можно использовать в сценариях незначительной трансформации. Например, [см. определение навыков 4](#transformation-example).

## <a name="skill-inputs"></a>Входные данные навыков
Во входных данных учитывается регистр.

| Входные данные   | Описание |
|-------------|-------------|
| condition   | Этот вход является [оценочным полем,](#evaluated-fields) которое представляет условие для оценки. Это условие должно оценить на значение Boolean *(истинное* или *ложное).*   <br/>  Примеры: <br/> "Правда" <br/> "Кв.(/документ/язык) <br/> "В $(/документ/страницы/\*/язык) - $(/документ/ожидаемыйязыкязык)" <br/> |
| когдаПравда    | Этот вход является [оцененным полем,](#evaluated-fields) которое представляет значение для возврата, если условие оценивается как *истинное.* Строки константы должны быть возвращены в отдельных кавычках (и '). <br/>Значения примеров: <br/> "Контракт"<br/>"За $(/документ/контрактType)" <br/> "Кв.) (/документ/сущности/\*)" <br/> |
| когдаЛожь   | Этот вход является [оцененным полем,](#evaluated-fields) которое представляет значение для возврата, если условие оценивается как *ложное.* <br/>Значения примеров: <br/> "Контракт"<br/>"За $(/документ/контрактType)" <br/> "Кв.) (/документ/сущности/\*)" <br/>

## <a name="skill-outputs"></a>Выходные данные навыка
Есть один выход, который просто называется "выход". Он возвращает *значение, когдаFalse,* если условие является ложным или *когдаTrue,* если условие верно.

## <a name="examples"></a>Примеры

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Определение навыков образца 1: Фильтр документы для возвращения только французских документов

Следующий вывод возвращает массив предложений ("/документ/французский приговоры"), если язык документа французский. Если язык не французский, значение устанавливается *в нулевых*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Если "/документ/французскиепредложения" используется в качестве *контекста* другого навыка, этот навык выполняется только в том случае, если "/документ/французскийпредложения" не будет сведены на *нет.*


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Определение навыка образца 2: Установите значение по умолчанию для значения, которое не существует

Следующий вывод создает аннотацию ("/документ/языкWithDefault"), которая устанавливается на язык документа или на "es", если язык не установлен.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Определение навыка образца 3: Значение слияния из двух полей в одно

В этом примере некоторые предложения имеют свойство *frenchSentiment.* Всякий раз, когда свойство *frenchSentiment* является недействительным, мы хотим использовать *значение englishSentiment.* Мы назначаем выход участнику, который называется *«чувство»* («документ/чувство/чувство/чувство»).

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Пример трансформации
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Определение навыков образца 4: Преобразование данных на одном поле

В этом примере мы получаем *чувство,* которое составляет от 0 до 1. Мы хотим, чтобы превратить его в период между -1 и 1. Мы можем использовать условный навык, чтобы сделать это незначительное преобразование.

В этом примере мы не используем условный аспект навыка, потому что условие всегда *верно.*

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Специальные рекомендации
Некоторые параметры оцениваются, поэтому необходимо быть особенно осторожным, чтобы следовать документально подтвержденной схеме. Выражения должны начинаться с равного знака. Путь должен быть разграничен "$(" и "))... Убедитесь в том, чтобы положить строки в отдельных кавычек. Это помогает оценщику различать строки и фактические пути и операторов. Кроме того, не забудьте разместить белое пространство вокруг операторов (например, «я» в пути означает нечто иное, чем умножить).


## <a name="next-steps"></a>Дальнейшие действия

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
