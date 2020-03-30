---
title: Ссылка на функции OData search.in
titleSuffix: Azure Cognitive Search
description: Синтаксис и справочная документация для использования функции search.in в запросах Azure Cognitive Search.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113120"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Функция `search.in` OData в когнитивном поиске Azure

Распространенным сценарием в [выражениях фильтра OData](query-odata-filter-orderby-syntax.md) является проверка того, является ли одно поле в каждом документе равным одному из многих возможных значений. Например, в некоторых приложениях выполняется [обрезка безопасности,](search-security-trimming-for-azure-search.md) проверяя поле, содержащее один или несколько основных инти-изданий, со списком основных интриаций, представляющих пользователя, выдающего запрос. Один из способов написать такой запрос [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) — использовать оператора и операторов:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Тем не менее, есть более короткий `search.in` способ написать это, используя функцию:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Помимо того, что короче `search.in` и проще для чтения, использование также обеспечивает [преимущества производительности](#bkmk_performance) и позволяет избежать [определенных ограничений размера фильтров,](search-query-odata-filter.md#bkmk_limits) когда Есть сотни или даже тысячи значений, чтобы включить в фильтр. По этой причине мы `search.in` настоятельно рекомендуем использовать вместо более сложного разъединения выражений равенства.

> [!NOTE]
> Версия 4.01 стандарта OData недавно представила [ `in` оператора,](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)который `search.in` имеет аналогичное поведение, как функция в Azure Cognitive Search. Однако Azure Cognitive Search не поддерживает этого оператора, поэтому вы должны использовать эту функцию. `search.in`

## <a name="syntax"></a>Синтаксис

Следующая форма EBNF[(Расширенная форма Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)определяет грамматику функции: `search.in`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Функция `search.in` проверяет, является ли заданная строка поля или диапазона переменной равна одному из данного списка значений. Равенство между переменной и каждым значением в списке определяется в случае `eq` чувствительных образом, так же, как для оператора. Следовательно, выражение, подобное `search.in(myfield, 'a, b, c')`, эквивалентно выражению `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, за исключением того, что функция `search.in` выполняется намного быстрее.

Есть две перегрузки `search.in` функции:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Параметры определены в следующей таблице:

| Имя параметра | Тип | Описание |
| --- | --- | --- |
| `variable` | `Edm.String` | Ссылка на поле строки (или переменная диапазона `search.in` по полю `any` `all` сбора строк в случае, когда используется внутри или выражения). |
| `valueList` | `Edm.String` | Строка, содержащая делимитированный список `variable` значений, чтобы соответствовать параметру. Если `delimiters` параметр не указан, делимитемы по умолчанию являются пространством и запятой. |
| `delimiters` | `Edm.String` | Строка, в которой каждый символ рассматривается как сепаратор при разборе `valueList` параметра. Значение этого параметра `' ,'` по умолчанию означает, что любые значения с пробелами и/или запятыми между ними будут разделены. Если вам нужно использовать сепараторы, кроме пробелов и запятых, потому что `'|'` ваши значения включают эти символы, вы можете указать альтернативные делимитеры, например, в этом параметре. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Производительность`search.in`

При использовании `search.in` время отклика будет менее секунды, если второй параметр содержит список из сотен или тысяч значений. Нет явного ограничения на количество `search.in`элементов, которые вы можете передать, хотя вы все еще ограничены максимальным размером запроса. Однако задержка растет по мере увеличения количества значений.

## <a name="examples"></a>Примеры

Найдите все отели с названием, равным мотеле «Морской вид», либо «Бюджетному отелю». Фразы содержат пробелы, которые по умолчанию разграничеки. Можно указать альтернативный делимитер в одной кавыки в качестве третьего параметра строки:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Найдите все отели с названием, равным мотеле «Морской вид», либо «Бюджетному отелю», разделенным «Кью»):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Найти все отели с номерами, которые имеют тег "Wi-Fi" или "труба":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Найдите совпадение по фразам в коллекции, таким как «нагретые стеллажи для полотенец» или «включаемые феном» в теги.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Найти все отели без тега "мотель" или "кабина":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
