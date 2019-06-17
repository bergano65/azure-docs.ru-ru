---
title: Выберите ссылку OData - поиска Azure
description: Справочник по языку OData для выберите синтаксис в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079682"
---
# <a name="odata-select-syntax-in-azure-search"></a>Синтаксис OData $select в службе поиска Azure

 Можно использовать [OData **$select** параметр](query-odata-filter-orderby-syntax.md) для выбора полей, включаемых в результаты поиска из поиска Azure. В этой статье описывается синтаксис **$select** подробно. Более общие сведения об использовании **$select** при представлении результатов поиска, см. в разделе [способы работы со службой поиска результатов в службе поиска Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Синтаксис

**$Select** параметр определяет, какие поля для каждого документа, возвращаются в результирующем наборе запроса. Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для **$select** параметр:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

**$Select** параметр бывают двух видов:

1. Один символ звездочки (`*`), означает, что все извлекаемые поля должны быть возвращены, или
1. Разделенный запятыми список путей поля, определение поля, которые должны возвращаться.

Если вы используете второй форме, можно указать только извлекаемые поля в списке.

Если вывести сложных поля без явного указания его вложенные поля, все извлекаемые вложенные поля будут включены в результирующий набор запроса. Предположим, например, в индексе есть `Address` с `Street`, `City`, и `Country` дополнительные поля, которые извлекаются все. Если указать `Address` в **$select**, результаты запроса будут включены все три вложенные поля.

## <a name="examples"></a>Примеры

Включить `HotelId`, `HotelName`, и `Rating` поля верхнего уровня в результатах, а также `City` подзапроса поле `Address`:

    $select=HotelId, HotelName, Rating, Address/City

В примере результат может выглядеть следующим образом:

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

Включить `HotelName` полей более высокого уровня в результаты, а также все вложенные поля `Address`и `Type` и `BaseRate` вложенные поля каждого объекта в `Rooms` коллекции:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

В примере результат может выглядеть следующим образом:

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

- [Как работать со службой поиска результатов в службе поиска Azure](search-pagination-page-layout.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
