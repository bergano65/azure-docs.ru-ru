---
title: Справочник по операторам коллекции OData
titleSuffix: Azure Cognitive Search
description: Операторы коллекции OData, любые и все лямбда-выражения в Azure Когнитивный поиск запросы.
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
ms.openlocfilehash: fdb250a844b70cef4f6941debbb1fa7450874932
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793401"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Операторы коллекции OData в Azure Когнитивный поиск — `any` и `all`

При написании [выражения фильтра OData](query-odata-filter-orderby-syntax.md) для использования с Azure когнитивный поиск часто бывает полезно выполнить фильтрацию по полям коллекции. Это можно сделать с помощью операторов `any` и `all`.

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику выражения OData, которая использует `any` или `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

Существуют три формы выражения, которые фильтруют коллекции.

- Первые два прохода по полю коллекции, применяя предикат, заданный в виде лямбда-выражения к каждому элементу коллекции.
  - Выражение, использующее `all`, возвращает `true`, если предикат имеет значение true для каждого элемента коллекции.
  - Выражение, использующее `any`, возвращает `true`, если предикат имеет значение true по крайней мере для одного элемента коллекции.
- Третья форма фильтра коллекции использует `any` без лямбда-выражения, чтобы проверить, пусто ли поле коллекции. Если коллекция содержит какие бы то ни было элементы, она возвращает `true`. Если коллекция пуста, она возвращает `false`.

**Лямбда-выражение** в фильтре коллекции аналогично телу цикла в языке программирования. Он определяет переменную, называемую **переменной диапазона**, которая содержит текущий элемент коллекции во время итерации. Он также определяет другое логическое выражение, которое является критерием фильтра для применения к переменной диапазона для каждого элемента коллекции.

## <a name="examples"></a>Примеры

Сопоставление документов, поле `tags` которых содержит только строку "Wi-Fi":

    tags/any(t: t eq 'wifi')

Сопоставление документов, в которых каждый элемент поля `ratings` находится в диапазоне от 3 до 5 включительно:

    ratings/all(r: r ge 3 and r le 5)

Сопоставление документов, где любая из географических координат в поле `locations` находится в пределах заданного многоугольника:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Сопоставить документы, в которых поле `rooms` пусто:

    not rooms/any()

Искать документы, где для всех помещений поле `rooms/amenities` содержит "TV", а `rooms/baseRate` меньше 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ограничения

Не все функции выражений фильтров доступны внутри тела лямбда-выражения. Ограничения различаются в зависимости от типа данных поля коллекции, которое необходимо отфильтровать. В следующей таблице перечислены ограничения.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Дополнительные сведения об этих ограничениях, а также примеры см. [в разделе Устранение неполадок фильтров сбора в Azure когнитивный Поиск](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему эти ограничения существуют, см. в разделе [Основные сведения о фильтрах коллекций в когнитивный Поиск Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в Когнитивный поиск Azure](search-filters.md)
- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Когнитивный поиск Azure REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
