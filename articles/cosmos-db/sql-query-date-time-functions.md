---
title: Функции даты и времени в Azure Cosmos DB языке запросов
description: Сведения о системных функциях SQL даты и времени в Azure Cosmos DB для выполнения операций DateTime и timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246943"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Функции даты и времени (Azure Cosmos DB)

Функции даты и времени позволяют выполнять операции DateTime и timestamp в Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Функции для получения даты и времени

Следующие скалярные функции позволяют получить текущую дату и время в формате UTC в двух формах: строку, которая соответствует формату ISO 8601, или числовой метке времени, значением которой является эпоха UNIX в миллисекундах:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Функции для работы со значениями DateTime

Следующие функции позволяют легко управлять значениями типа DateTime:

* [датетимеадд](sql-query-datetimeadd.md)
* [датетимедифф](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
