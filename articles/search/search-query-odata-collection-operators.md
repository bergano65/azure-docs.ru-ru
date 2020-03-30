---
title: Ссылка оператора по сбору данных OData
titleSuffix: Azure Cognitive Search
description: При создании выражений фильтра в запросах Azure Cognitive Search используйте "любых" и "всех" операторов в выражениях lambda, когда фильтр находится на поле сбора или сложном поле сбора.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113233"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Операторы сбора oData в `any` Azure Cognitive Search - и`all`

При написании [выражения фильтра OData](query-odata-filter-orderby-syntax.md) для использования в Azure Cognitive Search часто полезно фильтровать на полях сбора. Вы можете достичь `any` этого `all` с помощью и операторов.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику выражения OData, которое использует `any` или `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Существует три формы выражения, которые фильтруют коллекции.

- Первые два итерата над полем сбора, применяя предикат, приведенный в виде выражения лямбды к каждому элементу коллекции.
  - Выражение с `all` `true` использованием возвращается, если предикат верен каждому элементу коллекции.
  - Выражение с `any` `true` использованием возвращает, если предикат верен по крайней мере для одного элемента коллекции.
- Третья форма фильтра `any` сбора использует без выражения лямбда, чтобы проверить, пусто ли поле коллекции. Если коллекция имеет какие-либо `true`элементы, она возвращается. Если коллекция пуста, `false`она возвращается.

**Выражение лямбды** в фильтре коллекции подобно телу цикла на языке программирования. Он определяет переменную, называемую **переменной диапазона,** которая содержит текущий элемент коллекции во время итерации. Он также определяет другое выражение boolean которое критерии фильтра для того чтобы примениться к переменной ряда для каждого элемента собрания.

## <a name="examples"></a>Примеры

Документы `tags` матча, поле которых содержит именно строку "wifi":

    tags/any(t: t eq 'wifi')

Документы матча, где `ratings` каждый элемент поля падает между 3 и 5, включительно:

    ratings/all(r: r ge 3 and r le 5)

Совпадение документов, где любой из геокоординат в `locations` поле находится в пределах данного полигона:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Документы матча, `rooms` где поле пусто:

    not rooms/any()

Документы матча, где `rooms/amenities` для всех номеров, поле содержит "тв" и `rooms/baseRate` менее 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ограничения

Не каждая особенность выражения фильтра доступна внутри тела выражения лямбда. Ограничения различаются в зависимости от типа поля сбора данных, которое требуется отфильтровать. В следующей таблице кратко излагаются ограничения.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Более подробную информацию об этих ограничениях, а также примеры можно найти [в Azure Cognitive Search,](search-query-troubleshoot-collection-filters.md)см. Более подробную информацию о том, почему существуют эти ограничения, можно найти [в Azure Cognitive Search.](search-query-understand-collection-filters.md)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
