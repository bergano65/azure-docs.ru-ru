---
title: Квалификации условного cognitive search (поиск Azure) | Документация Майкрософт
description: Условного навык обеспечивает фильтрацию, создание по умолчанию и объединения значений.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791522"
---
#   <a name="conditional-skill"></a>Условный навыков

*Условного навыков* поддерживает сценарии поиска Azure, которые требуют логическую операцию для определения данных, чтобы назначить выходные данные. Эти сценарии включают фильтрацию, присваивание значения по умолчанию и объединение данных, на основе условия.

Следующий псевдокод демонстрирует выполняет условный навыков:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Этот навык не привязан к Azure Cognitive Services API, и вас не взимается плата по ее использованию. Тем не менее, все равно следует [присоединить ресурса Cognitive Services](cognitive-search-attach-cognitive-services.md) для переопределения параметра ресурсов уровня «бесплатный», которая ограничивает возможность небольшое количество усовершенствования в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Вычисленный поля

Этот навык как специальный, так как его входные данные являются вычисленного поля.

Перечисленные ниже приведены допустимые значения выражения.

-   Аннотация пути (путей в выражениях, которые должны быть заключены в «$("and")»)
 <br/>
    Примеры:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Литералы (строки, числа, значение true, false, null) <br/>
    Примеры:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Выражения, использующие операторы сравнения (==,! =, > =, >, < =, <) <br/>
    Примеры:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Выражения, использующие логические операторы (& &, ||,!, ^) <br/>
    Примеры:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Выражения, использующие числовые операторы (+, -, \*, /, %) <br/>
    Примеры: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Поскольку условного навык поддерживает вычисление, его можно использовать в сценариях minor преобразования. Например, см. в разделе [навыков определений 4](#transformation-example).

## <a name="skill-inputs"></a>Входные данные навыков
Во входных данных учитывается регистр.

| Вход   | Описание |
|-------------|-------------|
| условие   | Это входные данные — [вычислить поле](#evaluated-fields) , представляющий условие для вычисления. Это условие должно быть логическое значение (*true* или *false*).   <br/>  Примеры: <br/> «= true» <br/> «= $(/document/language) «fr» ==» <br/> «= $(/ / страницдокумента/ \* /Language) == $(/ документа/expectedLanguage)» <br/> |
| whenTrue    | Это входные данные — [вычислить поле](#evaluated-fields) , представляющий значение, возвращаемое, если условие оценено как *true*. Константы строки должны возвращаться в одинарные кавычки («"и»"). <br/>Примеры значений: <br/> «= «Контракт»»<br/>«= $(/ документа/contractType)» <br/> «= $(/ документа илисущности/\*)» <br/> |
| whenFalse   | Это входные данные — [вычислить поле](#evaluated-fields) , представляющий значение, возвращаемое, если условие оценено как *false*. <br/>Примеры значений: <br/> «= «Контракт»»<br/>«= $(/ документа/contractType)» <br/> «= $(/ документа илисущности/\*)» <br/>

## <a name="skill-outputs"></a>Выходные данные навыка
Есть один выход, он просто называется «выход». Возвращает значение *whenFalse* Если условие равно false или *whenTrue* Если условие истинно.

## <a name="examples"></a>Примеры

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Пример определения квалификации 1: Фильтровать документы для возврата только документы, французский

Следующие выходные данные возвращает массив предложений («/ документа/frenchSentences»), если установлен французский язык документа. Если язык не французский, присваивается значение *null*.

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
Если «/ документа/frenchSentences» используется в качестве *контекст* другой навыков, навыка, выполняется только в тех случаях, если не задано «/ документа/frenchSentences» *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Пример определения навыков 2: Значение по умолчанию значения, которое не существует

Следующие выходные данные создает комментария («/ документа/languageWithDefault»), который задается язык документа или «es», если язык не задан.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Пример определения навыков 3: Объединить значения из двух полей

В этом примере есть некоторые предложения *frenchSentiment* свойство. Каждый раз, когда *frenchSentiment* свойство имеет значение null, мы хотим использовать *englishSentiment* значение. Мы назначьте выходные данные к элементу, который называется *тональности* ("и документирование/тональности / * / тональности»).

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

## <a name="transformation-example"></a>Пример преобразования
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Пример определения навыков 4: Преобразование данных по одному полю

В этом примере мы получаем *тональности* от 0 до 1. Мы хотим преобразовывать его, чтобы быть в диапазоне от -1 до 1. Мы используем условного навык, для выполнения этого преобразования незначительные.

В этом примере мы не используем условного аспектом навык, так как условие всегда имеет *true*.

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
Некоторые параметры вычисляются, поэтому вам нужно быть особенно осторожны, стоит следовать этому шаблону документированных. Выражения должно начинаться со знака равенства. Пути должны быть разделены «$("and")». Убедитесь в том, что для размещения строк в одинарные кавычки. Который позволяет различать строки и фактические пути и операторы настраиваемый фильтр. Кроме того, обязательно добавьте пробелы вокруг операторов (например, «*» в пути означает, что-то отличается от multiply).


## <a name="next-steps"></a>Дальнейшие действия

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
