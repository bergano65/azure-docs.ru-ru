---
title: Выбор ссылки OData
titleSuffix: Azure Cognitive Search
description: Справочник по синтаксису и языку для явного выбора полей, возвращаемых в результатах поиска Когнитивный поиск запросов Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113100"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Синтаксис $select OData в Azure Когнитивный поиск

 [Параметр **$SELECT** OData](query-odata-filter-orderby-syntax.md) можно использовать для выбора полей, включаемых в результаты поиска когнитивный Поиск Azure. В этой статье подробно описывается синтаксис **$SELECT** . Дополнительные общие сведения об использовании **$SELECT** при представлении результатов поиска см. [в статье работа с результатами поиска в Azure когнитивный Поиск](search-pagination-page-layout.md).

## <a name="syntax"></a>Синтаксис

Параметр **$SELECT** определяет, какие поля для каждого документа возвращаются в результирующем наборе запроса. Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для параметра **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

Параметр **$SELECT** состоит из двух форм:

1. Одиночная звездочка`*`(), указывающая, что должны возвращаться все извлекаемые поля, или
1. Разделенный запятыми список путей к полям, определяющий, какие поля должны быть возвращены.

При использовании второй формы вы можете указать в списке только доступные для получения поля.

Если вы передаете сложное поле, не указывая его вложенные поля явным образом, все доступные для извлечения поля будут включаться в результирующий набор запроса. Например, предположим, что в индексе `Address` есть поле `Street`с `City`полями, `Country` и, и все они доступны для извлечения. Если указать `Address` в **$SELECT**, результаты запроса будут содержать все три вложенные поля.

## <a name="examples"></a>Примеры

Включите поля `HotelId`, `HotelName`и `Rating` верхнего уровня в результаты, а также `City` вспомогательное поле: `Address`

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

Включите поле `HotelName` верхнего уровня в результаты, а также все вложенные `Address`поля, и вложенные поля `Type` и `BaseRate` для каждого объекта в `Rooms` коллекции:

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

- [Работа с результатами поиска в Azure Когнитивный поиск](search-pagination-page-layout.md)
- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
