---
title: Функции даты и времени в Azure Cosmos DB языке запросов
description: Сведения о системных функциях SQL даты и времени в Azure Cosmos DB для выполнения операций DateTime и timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224957"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Функции даты и времени (Azure Cosmos DB)

Функции даты и времени позволяют выполнять операции DateTime и timestamp в Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Функции для получения даты и времени

Следующие скалярные функции позволяют получить текущую дату и время в формате UTC в двух формах: строку, которая соответствует формату ISO 8601, или числовой метке времени, значением которой является эпоха UNIX в миллисекундах:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [жеткурренттиккс](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Функции для работы со значениями DateTime

Следующие функции позволяют легко управлять значениями типа DateTime:

* [датетимеадд](sql-query-datetimeadd.md)
* [датетимедифф](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [датетимепарт](sql-query-datetimepart.md)
* [датетиметотиккс](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
