---
title: Функции проверки типов в языке запросов Azure Cosmos DB
description: Сведения о проверке типов системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349078"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Функции проверки типов (Azure Cosmos DB)

Функции проверки типов позволяют проверить тип выражения в SQL-запросе. Функции проверки типов можно использовать для определения типов свойств в элементах на лету, когда они являются переменными или неизвестными. 

## <a name="functions"></a>Функции

Ниже приведена таблица поддерживаемых встроенных функций проверки типов:

Следующие функции поддерживают проверку типа входных значений и возвращают логическое значение.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Дальнейшие действия

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
