---
title: ACOS на языке запросов Azure Cosmos DB
description: Сведения о том, как системная функция SQL ACOS (арккосице) в Azure Cosmos DB возвращает угол в радианах, косинус которого является указанным числовым выражением
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873528"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Возвращает угол в радианах, косинус которого равен указанному числовому выражению; также называется арккосинусом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается `ACOS`-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Результирующий набор:  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
