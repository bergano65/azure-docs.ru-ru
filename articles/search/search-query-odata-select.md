---
title: Справочник по SELECT OData — Поиск Azure
description: Справочник по языку OData для синтаксиса SELECT в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647873"
---
# <a name="odata-select-syntax-in-azure-search"></a>Синтаксис $select OData в поиске Azure

 [Параметр **$SELECT** OData](query-odata-filter-orderby-syntax.md) можно использовать для выбора полей, включаемых в результаты поиска в службе поиска Azure. В этой статье подробно описывается синтаксис **$SELECT** . Дополнительные общие сведения об использовании **$SELECT** при представлении результатов поиска см. [в статье как работать с результатами поиска в службе поиска Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Синтаксис

Параметр **$SELECT** определяет, какие поля для каждого документа возвращаются в результирующем наборе запроса. Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для параметра **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

Параметр **$SELECT** состоит из двух форм:

1. Одиночная звездочка`*`(), указывающая, что должны возвращаться все извлекаемые поля, или
1. Разделенный запятыми список путей к полям, определяющий, какие поля должны быть возвращены.

При использовании второй формы вы можете указать в списке только доступные для получения поля.

Если вы передаете сложное поле, не указывая его вложенные поля явным образом, все доступные для извлечения поля будут включаться в результирующий набор запроса. Например, предположим, что в индексе `Address` есть поле `Street`с `City`полями, `Country` и, и все они доступны для извлечения. Если указать `Address` в **$SELECT**, результаты запроса будут содержать все три вложенные поля.

## <a name="examples"></a>Примеры

`HotelId`Включите поля, `HotelName`и `Rating` верхнего уровня `Address`врезультаты, а также вспомогательноеполе:`City`

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

`Rooms` `BaseRate` `Type` `Address`Включите поле верхнегоуровняврезультаты,атакжевсевложенныеполя,ивложенныеполяидлякаждогообъектавколлекции:`HotelName`

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

## <a name="next-steps"></a>Следующие шаги  

- [Как работать с результатами поиска в службе "Поиск Azure"](search-pagination-page-layout.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
