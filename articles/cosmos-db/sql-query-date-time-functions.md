---
title: Функции даты и времени в Azure Cosmos DB языке запросов
description: Сведения о системных функциях SQL даты и времени в Azure Cosmos DB для выполнения операций DateTime и timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605187"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Функции даты и времени (Azure Cosmos DB)

Функции даты и времени позволяют выполнять операции DateTime и timestamp в Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Функции для получения даты и времени

Следующие скалярные функции позволяют получить текущую дату и время в формате UTC в трех формах: строку, которая соответствует формату ISO 8601, числовой временной отметке, значением которого является число миллисекунд, прошедших с момента создания эпохи UNIX, или числовые импульсы, значение которых равно количеству 100 наносекундных тактов, прошедших с момента создания эпохи UNIX. :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Функции для работы со значениями DateTime

Следующие функции позволяют легко управлять значениями типа DateTime, timestamp и Tick:

* [датетимеадд](sql-query-datetimeadd.md)
* [датетимедифф](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [датетимепарт](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [датетиметотиместамп](sql-query-datetimetotimestamp.md)
* [тиккстодатетиме](sql-query-tickstodatetime.md)
* [тиместамптодатетиме](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
