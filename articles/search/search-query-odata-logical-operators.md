---
title: Ссылка на логический оператор OData
titleSuffix: Azure Cognitive Search
description: Синтаксис и справочная документация для использования логических операторов OData, а также, или, или нет, в запросах Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113187"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Логические операторы OData в `and` `or`Azure Cognitive Search -`not`

[Выражения фильтра OData](query-odata-filter-orderby-syntax.md) в Azure Cognitive Search являются `true` `false`выражениями Boolean, которые оценивают или . Вы можете написать сложный фильтр, написав серию [простых фильтров](search-query-odata-comparison-operators.md) и составляя их с помощью логических операторов из [Allean algebra:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: Двоичный оператор, `true` который оценивает, если его левая `true`и правая суб-выражения оценить.
- `or`: Двоичный оператор, `true` который оценивает, если либо один из его `true`левых или правых суб-выражений оценивает .
- `not`: Неат-оператор, `true` который оценивает, если его `false`подвыражение оценивает, и наоборот.

Они, вместе с [операторами `any` сбора и, `all` ](search-query-odata-collection-operators.md)позволяют создавать фильтры, которые могут выразить очень сложные критерии поиска.

## <a name="syntax"></a>Синтаксис

Следующая форма EBNF[(Расширенная форма Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)определяет грамматику выражения OData, использующее логических операторов.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Есть две формы логических выражений: двоичный (`and`/`or`), где есть`not`два суб-выражений, и неарий ( ), где есть только один. Подвыражениями могут быть булейские выражения любого рода:

- Поля или переменные диапазона типа`Edm.Boolean`
- Функции, возвращающие `Edm.Boolean`значения типа, такие как `geo.intersects` или`search.ismatch`
- [Выражения сравнения,](search-query-odata-comparison-operators.md)такие как`rating gt 4`
- [Выражения коллекции,](search-query-odata-collection-operators.md)такие как`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Boolean буквальные `true` или `false`.
- Другие логические выражения `and` `or`построены `not`с использованием , и .

> [!IMPORTANT]
> Есть некоторые ситуации, когда не все виды суб-выражения могут быть использованы с `and` / `or`, особенно внутри lambda выражений. Подробнее о [операторах сбора данных oData читайте в материале Azure Cognitive Search.](search-query-odata-collection-operators.md#limitations)

### <a name="logical-operators-and-null"></a>Логические операторы и`null`

Большинство выражений Boolean, таких как `null` функции и сравнения, не могут `null` производить значения, и `x and null` логические операторы не могут быть применены к буквальному непосредственно (например, не допускается). Тем не менее, `null`Boolean поля могут быть, `and`так `or`что `not` вы должны быть осведомлены о том, как , и операторы ведут себя в присутствии нуля. Это кратко в следующей таблице, где `b` поле `Edm.Boolean`типа:

| Выражение | Результат, `b` когда есть`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Когда `b` поле Boolean появляется само по себе в выражении фильтра, `b eq true`оно `b` ведет `null`себя так, как если бы оно было написано, так что если есть, то выражение оценивается в. `false` Аналогичным `not b` образом, ведет `not (b eq true)`себя как , `true`так что он оценивает . Таким образом, `null` поля ведут себя `false`так же, как и . Это согласуется с тем, как они ведут `and` `or`себя в сочетании с другими выражениями, использующими и, как показано в таблице выше. Несмотря на это, `false` `b eq false`прямое сравнение `false`с ( ) будет по-прежнему оценивать . Другими словами, `null` не `false`равна , даже если он ведет себя, как это в Boolean выражений.

## <a name="examples"></a>Примеры

Документы матча, `rating` где поле между 3 и 5, включительно:

    rating ge 3 and rating le 5

Документы соответствия, `ratings` где все элементы поля меньше, чем 3 или больше, чем 5:

    ratings/all(r: r lt 3 or r gt 5)

Совпадение `location` документов, где поле находится в пределах данного полигона, и документ не содержит термина "общественный".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Матч документы для отелей в Ванкувере, Канада, где есть номер повышенной комфортности с базовой ставкой менее 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
