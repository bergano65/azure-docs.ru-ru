---
title: Квалификации условного cognitive search (поиск Azure) | Документация Майкрософт
description: Условный навык, позволяющее фильтрацию, создавать значения по умолчанию и объединения значений.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028430"
---
#   <a name="conditional-skill"></a>Условный навыков

**Условного навыков** позволяет реализовать различные сценарии, требующие логическую операцию для решите, какие данные должны быть назначены выходные данные. К этим сценариям относятся: фильтрация, присваивание значения по умолчанию и объединение данных на основе условия.

Следующий псевдокод объясняет выполняет условный навыков:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Этот навык не привязан к API Cognitive Services, и за его использование плата не взимается. Но вам следует по-прежнему [подключать ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md), чтобы переопределить **бесплатную** категорию ресурса, ограниченную малым количеством обогащений в день.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Вычисленный поля

Этот навык как специальный, так как его входные данные являются вычисленного поля.

Ниже приведены допустимые значения выражения.

-   Аннотация пути (путей в выражениях, которые должны быть заключены в «$("and")») <br/>
    Примеры:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Литералы (строки, числа, значение true, false, null) <br/>
    Примеры:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Выражения, использующие оператор сравнения (==,! =, > =, >, < =, <) <br/>
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

-   Выражения, использующие числового оператора (+, -, \*, /, %) <br/>
    Примеры: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Из-за вычисление, которое поддерживается условного навык может использоваться для сценариев незначительные преобразования. Пример см. в разделе [навыков определений 4](#transformation-examples) пример.

## <a name="skill-inputs"></a>Входные данные навыков
Во входных данных учитывается регистр.

| Входные данные      | ОПИСАНИЕ |
|-------------|-------------|
| condition   | Это входные данные — [вычислить поле](#evaluated-fields) , представляющий условие для вычисления. Это условие должно быть логическое значение (true или false).   <br/>  Примеры: <br/> «= true» <br/> «= $(/document/language) «fr» ==» <br/> «= $(/ / страницдокумента/ \* /Language) == $(/ документа/expectedLanguage)» <br/> |
| whenTrue    | Это входные данные — [вычислить поле](#evaluated-fields). Значение, возвращаемое, если условие оценивается в значение true. Константы строки должен быть возвращен в "" кавычки. <br/>Примеры значений: <br/> «= «Контракт»»<br/>«= $(/ документа/contractType)» <br/> «= $(/ документа илисущности/\*)» <br/> |
| whenFalse   | Это входные данные — [вычислить поле](#evaluated-fields). Значение, возвращаемое, если условие оценивается в значение false.  <br/>Примеры значений: <br/> «= «Контракт»»<br/>«= $(/ документа/contractType)» <br/> «= $(/ документа илисущности/\*)» <br/>

## <a name="skill-outputs"></a>Выходные данные навыка
Есть один выход, называется «вывод». Возвращается значение whenFalse, если условие равно false, или whenTrue, если условие истинно.

## <a name="examples"></a>Примеры

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Пример определения квалификации 1: Фильтрация документов для возврата только «Французский» документов

Следующие выходные данные будут возвращать массив предложений («/ документа/frenchSentences»), если язык документа — французский. Если язык не французский, это значение устанавливается в значение null.

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
Если «/ документа/frenchSentences» используется в качестве *контекст* другой навыков, навыка, будет выполняться только если «/ документа/frenchSentences» не задано значение null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Пример определения навыков 2: Установка значения по умолчанию, когда он не существует.

Следующие выходные данные создаст комментария («/ документа/languageWithDefault»), который имеет значение язык документа, или «es», если язык не задан.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Пример определения навыков 3: Объединения значений из двух разных полей в одном поле

В этом примере есть некоторые предложения *frenchSentiment* свойство. Каждый раз, когда *frenchSentiment* свойство имеет значение null, мы бы хотели использовать *englishSentiment* значение. Мы назначьте выходные данные элемент с именем просто *тональности* ("и документирование/тональности / * / тональности»).

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

## <a name="transformation-examples"></a>Примеры преобразований
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Пример определения навыков 4: Выполнение преобразования данных на одно поле

В этом примере мы получаем тональности от 0 до 1, и мы бы хотели преобразует их, так как это от -1 до 1. Это небольшой math преобразования, что мы можем сделать с помощью условного навыков.

В этом примере мы никогда не использовать условный аспектом навык, так как условие всегда имеет значение true. 

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
Обратите внимание, что некоторые параметры вычисляются, поэтому необходимо быть особенно осторожны при следующем документированных формате. Выражения должно начинаться с equals знака «=» и пути должны быть разделены «$("and")». Убедитесь в том, что для перевода строк в «одинарные кавычки», поскольку это поможет средство оценки различать строки и фактические пути и операторы. Кроме того, обязательно добавьте пробелы вокруг операторов (например * в пути имеет смысл, отличный от оператор умножения).


## <a name="next-steps"></a>Дальнейшие действия

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
