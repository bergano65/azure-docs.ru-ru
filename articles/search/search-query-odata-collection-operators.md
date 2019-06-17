---
title: Справочник по операторам коллекции OData - поиска Azure
description: Операторы коллекции OData, все и лямбда-выражения в запросах поиска Azure.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079942"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Операторы коллекции OData в службе поиска Azure — `any` и `all`

При написании [критерий фильтра OData](query-odata-filter-orderby-syntax.md) для использования со службой поиска Azure, часто бывает полезно для фильтрации коллекции полей. Это можно сделать с помощью `any` и `all` операторы.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматика выражения, использующего OData `any` или `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

Существует три формы выражения, которые фильтруют коллекций.

- Первые два итерацию по коллекции поле, применение предиката, в виде лямбда-выражение к каждому элементу коллекции.
  - Выражение, использующее `all` возвращает `true` Если предикат имеет значение true, если для каждого элемента коллекции.
  - Выражение, использующее `any` возвращает `true` предикат имеет значение true хотя бы один элемент коллекции.
- Третья форма коллекции фильтрации использует `any` без лямбда-выражение, чтобы проверить, является ли пустые поля коллекции. Если коллекция содержит какие-либо элементы, он возвращает `true`. Если коллекция пуста, она возвращает `false`.

Объект **лямбда-выражение** в коллекции фильтр аналогичен тело цикла в языке программирования. Он определяет переменную, вызывается **переменная диапазона**, который содержит текущий элемент коллекции во время итерации. Он также определяет другой логическое выражение условия фильтра, чтобы применить к переменной диапазона для каждого элемента коллекции.

## <a name="examples"></a>Примеры

Совпадение документы, у которых `tags` поле содержит ровно строку «wifi»:

    tags/any(t: t eq 'wifi')

Совпадение документы, где каждый элемент `ratings` поле относится от 3 до 5 включительно:

    ratings/all(r: r ge 3 and r le 5)

Совпадение документов, где любой из географически координаты в `locations` поле находится в пределах указанного многоугольника:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Совпадение Документирует where `rooms` поле пусто:

    not rooms/any()

Соответствует документов там, где для всех комнат `rooms/amenities` поле содержит «tv» и `rooms/baseRate` меньше 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ограничения

Не все функции, выражения фильтров доступна в теле лямбда-выражения. Ограничения зависят от типа данных поля коллекции, которое нужно выполнить фильтрацию. В следующей таблице перечислены ограничения.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Дополнительные сведения об этих ограничениях, а также примеры, см. в разделе [Устранение неполадок коллекции фильтров в поиске Azure](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему эти ограничения, существует, см. в разделе [Общие сведения о фильтрах коллекции в поиске Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
