---
title: CONCAT на языке запросов Azure Cosmos DB
description: Узнайте, как системная функция CONCAT SQL в Azure Cosmos DB возвращает строку, которая является результатом сцепления двух или более строковых значений.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871556"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Возвращает строку, являющуюся результатом объединения двух или более строковых значений.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение для объединения с другими значениями. Для функции `CONCAT` требуется по крайней мере два аргумента *str_expr* .  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает объединенную строку указанных значений.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Результирующий набор:  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
