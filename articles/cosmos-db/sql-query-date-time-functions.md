---
title: Функции даты и времени в Azure Cosmos DB языке запросов
description: Сведения о системных функциях SQL даты и времени в Azure Cosmos DB для выполнения операций DateTime и timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 055fd5182920ebe15cb2f3ed4b7a8ff69bb94c16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549573"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Функции даты и времени (Azure Cosmos DB)

Функции даты и времени позволяют выполнять операции DateTime и timestamp в Azure Cosmos DB.

## <a name="functions"></a>Функции

Следующие скалярные функции позволяют получить текущую дату и время в формате UTC в двух формах. числовая метка времени, значение которой является эпохой UNIX в миллисекундах или в виде строки, которая соответствует формату ISO 8601:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="next-steps"></a>Дальнейшие шаги

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
