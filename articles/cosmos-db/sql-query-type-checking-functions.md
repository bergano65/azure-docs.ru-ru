---
title: Функции проверки типов в языке запросов Azure Cosmos DB
description: Сведения о проверке типов системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bafc599ddb502d5714b08ff58ed942e9c17557a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093764"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Функции проверки типов (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Функции проверки типов позволяют проверить тип выражения в SQL-запросе. Функции проверки типов можно использовать для определения типов свойств в элементах на лету, когда они являются переменными или неизвестными. 

## <a name="functions"></a>Функции

Ниже приведена таблица поддерживаемых встроенных функций проверки типов:

Следующие функции поддерживают проверку типа входных значений и возвращают логическое значение.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические функции](sql-query-aggregates.md)
