---
title: Справочник по операторам коллекции OData — Поиск Azure
description: Операторы коллекции OData, любые и все лямбда-выражения в запросах поиска Azure.
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647638"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Операторы коллекции OData в службе поиска Azure `any` — и`all`

При написании [выражения фильтра OData](query-odata-filter-orderby-syntax.md) для использования с поиском Azure часто бывает полезно выполнить фильтрацию по полям коллекции. Это можно сделать с помощью `any` операторов и. `all`

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику выражения OData, в которой используется `any` или `all`.

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
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

Существуют три формы выражения, которые фильтруют коллекции.

- Первые два прохода по полю коллекции, применяя предикат, заданный в виде лямбда-выражения к каждому элементу коллекции.
  - Выражение, `all` использующее `true` , возвращает, если предикат имеет значение true для каждого элемента коллекции.
  - Выражение, `any` использующее `true` , возвращает, если предикат имеет значение true по крайней мере для одного элемента коллекции.
- Третья форма фильтра коллекции использует `any` без лямбда-выражения, чтобы проверить, пусто ли поле коллекции. Если коллекция содержит какие бы то ни было элементы `true`, она возвращает. Если коллекция пуста, она возвращает `false`.

**Лямбда-выражение** в фильтре коллекции аналогично телу цикла в языке программирования. Он определяет переменную, называемую **переменной диапазона**, которая содержит текущий элемент коллекции во время итерации. Он также определяет другое логическое выражение, которое является критерием фильтра для применения к переменной диапазона для каждого элемента коллекции.

## <a name="examples"></a>Примеры

Сопоставить документы, `tags` поле которых содержит только строку "Wi-Fi":

    tags/any(t: t eq 'wifi')

Сопоставление документов, в которых каждый элемент `ratings` поля находится в диапазоне от 3 до 5 включительно:

    ratings/all(r: r ge 3 and r le 5)

Сопоставление документов, где любая из географических координат в `locations` поле находится в пределах заданного многоугольника:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Сопоставить документы, в `rooms` которых поле пусто:

    not rooms/any()

Искать документы, `rooms/amenities` где для всех помещений поле содержит "TV" и `rooms/baseRate` меньше 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ограничения

Не все функции выражений фильтров доступны внутри тела лямбда-выражения. Ограничения различаются в зависимости от типа данных поля коллекции, которое необходимо отфильтровать. В следующей таблице перечислены ограничения.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Дополнительные сведения об этих ограничениях, а также примеры см. [в разделе Устранение неполадок фильтров сбора в службе поиска Azure](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему эти ограничения существуют, см. в разделе [Основные сведения о фильтрах коллекции в службе поиска Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Следующие шаги  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
