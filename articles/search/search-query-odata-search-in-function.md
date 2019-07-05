---
title: Справочник по функциям search.in OData - поиска Azure
description: Функция search.in OData в запросах поиска Azure.
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449983"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` функция в службе поиска Azure

Распространенный сценарий в [выражения фильтров OData](query-odata-filter-orderby-syntax.md) заключается в проверке, равен ли одно поле в каждом документе одно из многих возможных значений. Например, это, как реализовать некоторые приложения [фильтрация по ролям безопасности](search-security-trimming-for-azure-search.md) --путем проверки поле, содержащее один или несколько идентификаторы участников со списком идентификаторы участников, представляющий пользователя, выполняющего запрос. Один из способов написать запрос наподобие этого, можно выполнить [ `eq` ](search-query-odata-comparison-operators.md) и [ `or` ](search-query-odata-logical-operators.md) операторы:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Тем не менее, есть короткий способ записи, с помощью `search.in` функции:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Кроме того, что, короче и удобнее для чтения, с помощью `search.in` также предоставляет [выигрыш в производительности](#bkmk_performance) и позволяет избежать некоторых [размер ограничения фильтров](search-query-odata-filter.md#bkmk_limits) при наличии сотен или даже тысяч значений Чтобы включить в фильтр. По этой причине мы настоятельно рекомендуем использовать `search.in` вместо более сложные сложение выражений равенства.

> [!NOTE]
> Стандарт OData версии 4.01 недавно представила [ `in` оператор](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), который имеет аналогичное поведение как `search.in` функция в службе поиска Azure. Тем не менее, службы поиска Azure не поддерживает этот оператор, поэтому необходимо использовать `search.in` вместо этого функцию.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `search.in` функции:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

`search.in` Служит для проверки заданного строкового поля или переменная диапазона, равно одному из заданного списка значений. Равенство между переменной и каждое значение в списке определяется способом с учетом регистра, так же, как для `eq` оператор. Следовательно, выражение, подобное `search.in(myfield, 'a, b, c')`, эквивалентно выражению `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, за исключением того, что функция `search.in` выполняется намного быстрее.

Существуют две перегрузки `search.in` функции:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Параметры определяются в следующей таблице:

| Имя параметра | type | Описание |
| --- | --- | --- |
| `variable` | `Edm.String` | Ссылка на поле строки (или переменную диапазона для поля коллекции строку в случае где `search.in` используется внутри `any` или `all` выражение). |
| `valueList` | `Edm.String` | Строка, содержащая список с разделителями значения будет сравниваться с `variable` параметра. Если `delimiters` параметр не указан, по умолчанию к разделителям, пробел и запятая. |
| `delimiters` | `Edm.String` | Строка, где каждый символ обрабатывается как разделитель при синтаксическом анализе `valueList` параметра. Значение по умолчанию этого параметра — `' ,'` это означает, что все значения с пробелы и запятые между ними будет разделить. Если необходимо использовать разделители, отличные от пробелы и запятые, так как значения включают эти символы, можно указать альтернативные разделители, такие как `'|'` в этом параметре. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Производительность `search.in`

При использовании `search.in` время отклика будет менее секунды, если второй параметр содержит список из сотен или тысяч значений. Нет явной ограничений на число элементов, которые можно передать `search.in`, несмотря на то, что вы по-прежнему ограничены максимальный размер запроса. Однако задержка растет по мере увеличения количества значений.

## <a name="examples"></a>Примеры

Найти все гостиницы с именем, равным «Представление Sea motel» или «Бюджета hotel». Фразы должны содержать пробелы, который является разделителем по умолчанию. Можно указать альтернативные разделитель в одинарные кавычки в качестве третьего параметра строки:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Найти все гостиницы с именем, равным «Представление Sea motel» или «Бюджета hotel», разделенные "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Найти все гостиницы с помещениях, имеющих тег «wifi» или «ванная»:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Найти совпадение для фразы в пределах коллекции, например «жаркие выкинуть стойках» или «hairdryer включены» в тегах.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Найти все гостиницы без тега «motel» и «камера»:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
