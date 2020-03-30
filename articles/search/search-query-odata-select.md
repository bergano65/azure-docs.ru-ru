---
title: OData выбрать ссылку
titleSuffix: Azure Cognitive Search
description: Синтаксис и языковая ссылка на явный выбор полей для возврата в результатах поиска запросов Azure Cognitive Search.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113100"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select синтаксис в когнитивном поиске Azure

 Можно использовать [параметр OData **$select,** ](query-odata-filter-orderby-syntax.md) чтобы выбрать, какие поля следует включить в результаты поиска из Azure Cognitive Search. В этой статье подробно описывается синтаксис **$select.** Более подробную информацию о том, как использовать **$select** при представлении результатов поиска, можно [найти в Azure Cognitive Search.](search-pagination-page-layout.md)

## <a name="syntax"></a>Синтаксис

Параметр **$select** определяет, какие поля для каждого документа возвращаются в наборе результатов запроса. Следующая форма EBNF[(Расширенная форма Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)определяет грамматику для **$select** параметра:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Параметр **$select** поставляется в двух формах:

1. Одна звезда`*`( , указывая, что все извлекаемые поля должны быть возвращены, или
1. Список полевых путей, разделенных запятой, определяющий, какие поля следует вернуть.

При использовании второй формы можно указать только извлекаемые поля в списке.

Если вы перечислите сложное поле без указания его подполей прямо, все извлекаемые подполя будут включены в набор результатов запроса. Например, `Address` предположим, `Street` `City`что у индекса есть поле с и `Country` подполями, которые можно извлечь. Если указать `Address` в **$select,** результаты запроса будут включать все три подполя.

## <a name="examples"></a>Примеры

`HotelId`Включите `HotelName`поля `Rating` , и топ-уровня поля в `City` результатах, `Address`а также подполе:

    $select=HotelId, HotelName, Rating, Address/City

Пример результата может выглядеть следующим образом:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Включите поле `HotelName` верхнего уровня в результаты, а `Address`также `Type` все `BaseRate` подполя, а также `Rooms` подполя каждого объекта в коллекцию:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Пример результата может выглядеть следующим образом:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия  

- [Как работать с результатами поиска в Azure Cognitive Search](search-pagination-page-layout.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
