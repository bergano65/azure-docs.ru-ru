---
title: Справочник по логическому оператору OData — Поиск Azure
description: Логические операторы OData, и, или, а не в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647997"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Логические операторы OData в службе поиска Azure `and`— `or`,,`not`

[Выражения фильтра OData](query-odata-filter-orderby-syntax.md) в поиске Azure — это логические выражения, которые `true` возвращают или `false`. Сложный фильтр можно написать, написав ряд [простых фильтров](search-query-odata-comparison-operators.md) и создав их с помощью логических операторов из последовательности [логических](https://en.wikipedia.org/wiki/Boolean_algebra)операций:

- `and`. Бинарный оператор, возвращающий `true` значение, если `true`результат вычисления левого и правого вложенных выражений равен.
- `or`. Бинарный оператор, возвращающий `true` значение, если либо одно из его левого или правого вложенного выражения `true`имеет значение.
- `not`. Унарный оператор, возвращающий `true` значение `false`, если его вложенное выражение имеет значение, и наоборот.

Они вместе с операторами [ `any` коллекций и `all` ](search-query-odata-collection-operators.md)позволяют создавать фильтры, которые могут выражать очень сложные условия поиска.

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
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

Существует две формы логических`and`выражений: binary (/`or`), где есть два подвыражения, и унарный (`not`), где имеется только один. Подвыражения могут быть логическими выражениями любого типа:

- Поля или переменные диапазона типа`Edm.Boolean`
- Функции, возвращающие значения `Edm.Boolean`типа, такие `geo.intersects` как или`search.ismatch`
- [Выражения сравнения](search-query-odata-comparison-operators.md), такие как`rating gt 4`
- [Выражения коллекций](search-query-odata-collection-operators.md), такие как`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Логические литералы `true` или `false`.
- Другие логические выражения, созданные `and`с `or`помощью, `not`и.

> [!IMPORTANT]
> Существуют ситуации, когда не все виды вложенных выражений можно использовать с `and` / `or`, особенно внутри лямбда-выражений. Дополнительные сведения см. [в разделе операторы коллекции OData в службе поиска Azure](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Логические операторы и`null`

Большинство логических выражений, таких как функции и сравнения, не `null` могут формировать значения, а логические операторы не могут применяться `null` к литералу напрямую (например, `x and null` не допускается). Однако логические `null`поля могут быть, поэтому необходимо знать, `and`как работают операторы, `or`и `not` при наличии значения NULL. Это приводится в следующей таблице, где `b` — поле типа: `Edm.Boolean`

| Выражение | Результат, `b` если имеет значение`null` |
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

Если `b` логическое поле присутствует в критерии фильтра, оно ведет себя так, как если бы оно было написано `b` `b eq true`, поэтому если имеет значение `null`, выражение принимает `false`значение. Аналогично `not b` , ведет себя как `not (b eq true)`, `true`поэтому он принимает значение. Таким образом, `null` поля ведут себя так же, `false`как. Это согласуется с тем, как они работают при объединении с другими `and` выражениями с помощью и `or`, как показано в таблице выше. Несмотря на это, прямое сравнение `false` (`b eq false` `false`) по-прежнему будет иметь значение. Иными словами, `null` не `false`равно, даже несмотря на то, что он работает как в логических выражениях.

## <a name="examples"></a>Примеры

Сопоставление документов, в `rating` которых поле находится в диапазоне от 3 до 5 включительно:

    rating ge 3 and rating le 5

Сопоставление документов, в `ratings` которых все элементы поля имеют значение меньше 3 или больше 5:

    ratings/all(r: r lt 3 or r gt 5)

Сопоставление документов, в `location` которых поле находится внутри заданного многоугольника, и документ не содержит термин "общедоступный".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Сопоставьте документы для гостиниц в Vancouver, Канаде, где имеется комната Deluxe с базовой частотой менее 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Следующие шаги  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
