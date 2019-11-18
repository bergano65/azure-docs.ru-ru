---
title: Справочник по логическому оператору OData
titleSuffix: Azure Cognitive Search
description: Справочная документация по синтаксису и использованию логических операторов OData, и, или, а не в запросах Azure Когнитивный поиск.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113187"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Логические операторы OData в Azure Когнитивный поиск — `and`, `or`, `not`

[Выражения фильтра OData](query-odata-filter-orderby-syntax.md) в когнитивный Поиск Azure — это логические выражения, которые оцениваются как `true` или `false`. Сложный фильтр можно написать, написав ряд [простых фильтров](search-query-odata-comparison-operators.md) и создав их с помощью логических операторов из последовательности [логических](https://en.wikipedia.org/wiki/Boolean_algebra)операций:

- `and`: бинарный оператор, результатом которого является `true`, если оба его левого и правого вложенных выражения имеют `true`.
- `or`: бинарный оператор, который вычисляет значение `true`, если одно из его левых или правых выражений имеет значение `true`.
- `not`: унарный оператор, результатом которого является `true`, если его вложенное выражение имеет значение `false`, и наоборот.

Вместе с [операторами коллекций `any` и `all`](search-query-odata-collection-operators.md)позволяют создавать фильтры, которые могут выразить очень сложные условия поиска.

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику выражения OData, в которой используются логические операторы.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

Существует две формы логических выражений: binary (`and`/`or`), где есть два подвыражения и унарный (`not`), где имеется только один. Подвыражения могут быть логическими выражениями любого типа:

- Поля или переменные диапазона типа `Edm.Boolean`
- Функции, возвращающие значения типа `Edm.Boolean`, такие как `geo.intersects` или `search.ismatch`
- [Выражения сравнения](search-query-odata-comparison-operators.md), такие как `rating gt 4`
- [Выражения коллекции](search-query-odata-collection-operators.md), такие как `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Логические литералы `true` или `false`.
- Другие логические выражения, созданные с помощью `and`, `or`и `not`.

> [!IMPORTANT]
> Существуют ситуации, когда не все виды вложенных выражений можно использовать с `and`/`or`, особенно внутри лямбда-выражений. Дополнительные сведения см. [в разделе операторы коллекции OData в когнитивный Поиск Azure](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Логические операторы и `null`

Большинство логических выражений, таких как функции и сравнения, не могут формировать `null` значений, а логические операторы не могут применяться к литералу `null` напрямую (например, `x and null` не разрешено). Однако логические поля можно `null`, поэтому необходимо знать, как работают операторы `and`, `or`и `not` при наличии значения NULL. Это приводится в следующей таблице, где `b` является полем типа `Edm.Boolean`:

| Выражение | Результат, когда `b` `null` |
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

Если логическое поле `b` отображается само по себе в критерии фильтра, оно ведет себя так, как если бы оно было написано `b eq true`, поэтому если `b` `null`, выражение принимает значение `false`. Аналогичным образом `not b` ведет себя как `not (b eq true)`, поэтому он принимает значение `true`. Таким образом, `null` поля ведут себя так же, как `false`. Это согласуется с тем, как они работают при объединении с другими выражениями с использованием `and` и `or`, как показано в таблице выше. Несмотря на это, прямое сравнение `false` (`b eq false`) по-прежнему будет иметь `false`. Иными словами, `null` не равно `false`, хотя он работает так же, как в логических выражениях.

## <a name="examples"></a>Примеры

Сопоставление документов, в которых поле `rating` находится в диапазоне от 3 до 5 включительно:

    rating ge 3 and rating le 5

Сопоставление документов, в которых все элементы поля `ratings` меньше 3 или больше 5:

    ratings/all(r: r lt 3 or r gt 5)

Сопоставление документов, в которых поле `location` находится внутри заданного многоугольника, а документ не содержит термин "общедоступный".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Сопоставьте документы для гостиниц в Vancouver, Канаде, где имеется комната Deluxe с базовой частотой менее 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Дополнительная информация  

- [Фильтры в Когнитивный поиск Azure](search-filters.md)
- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Когнитивный поиск Azure REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
