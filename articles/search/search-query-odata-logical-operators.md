---
title: Справочник по логическим оператором OData — поиска Azure
description: Логические операторы OData и, или и, в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079773"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Логические операторы OData в службе поиска Azure — `and`, `or`, `not`

[Выражения фильтров OData](query-odata-filter-orderby-syntax.md) в службе поиска Azure — это логические выражения, которые дают `true` или `false`. Можно написать сложный фильтр, написав ряд [проще фильтры](search-query-odata-comparison-operators.md) и составления их с помощью логических операторов из [алгебры логическое значение](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Бинарный оператор, результатом которого является `true` Если оба его левого и правого вложенные выражения `true`.
- `or`: Бинарный оператор, результатом которого является `true` Если один из его вложенных выражений влево или вправо, результатом которого является `true`.
- `not`: Унарный оператор, результатом которого является `true` если его часть выражения, результатом которого является `false`и наоборот.

Их вместе с [коллекции операторов `any` и `all` ](search-query-odata-collection-operators.md), позволяют создавать фильтры, которые можно представить очень сложные условия поиска.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматика выражения OData, который использует логические операторы.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

Существует две формы логических выражений: двоичный (`and`/`or`), где есть два вложенных выражений и унарный (`not`), где имеется только один. Вложенные выражения могут быть логические выражения любого типа:

- Переменные типа поля или диапазона `Edm.Boolean`
- Функции, возвращающие значения типа `Edm.Boolean`, такие как `geo.intersects` или `search.ismatch`
- [Выражения сравнения](search-query-odata-comparison-operators.md), такие как `rating gt 4`
- [Коллекция выражений](search-query-odata-collection-operators.md), такие как `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Логические литералы `true` или `false`.
- Другие логические выражения, создано с помощью `and`, `or`, и `not`.

> [!IMPORTANT]
> Существуют ситуации, где не все виды часть выражения могут использоваться с `and` / `or`— особенно внутри лямбда-выражения. См. в разделе [операторы коллекции OData в службе поиска Azure](search-query-odata-collection-operators.md#limitations) подробные сведения.

### <a name="logical-operators-and-null"></a>Логические операторы и `null`

Большинство логических выражений, таких как функции и сравнения не могут производить `null` значения и логические операторы не может применяться к `null` литерал напрямую (например, `x and null` не допускается). Тем не менее, может быть логических полей `null`, поэтому необходимо иметь в виду того, как `and`, `or`, и `not` операторы работают при наличии значение null. Это показано в следующей таблице, где `b` является полем типа `Edm.Boolean`:

| Expression | Результат при `b` — `null` |
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

Если логическое поле `b` отображается сама по себе в выражении фильтра, он действует так, будто он написал `b eq true`, поэтому если `b` — `null`, выражение, результатом которого является `false`. Аналогичным образом `not b` ведет себя как `not (b eq true)`, поэтому он возвращает `true`. В этом случае `null` поля ведут себя так же, как `false`. Это согласуется с поведение при объединении с другими выражениями с помощью `and` и `or`, как показано в приведенной выше таблице. Несмотря на это, прямое сравнение с целью `false` (`b eq false`) по-прежнему будет оцениваться как `false`. Другими словами `null` не равно `false`, несмотря на то, что он ведет себя так, как и в логических выражениях.

## <a name="examples"></a>Примеры

Совпадение Документирует where `rating` поля — от 3 до 5 включительно:

    rating ge 3 and rating le 5

Совпадение документы, где все элементы `ratings` поля меньше 3 или больше 5:

    ratings/all(r: r lt 3 or r gt 5)

Совпадение Документирует where `location` поле находится в пределах указанного многоугольника, и документ не содержит термин «public».

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Документы для гостиниц в Ванкувер, Канада там, где deluxe места с базовым оценить меньше 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
