---
title: COT на языке запросов Azure Cosmos DB
description: Сведения о том, как системная функция SQL котангенса (COT) в Azure Cosmos DB возвращает тригонометрический котангенс указанного угла в радианах в указанном числовом выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871505"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Возвращает тригонометрический котангенс указанного угла в радианах в указанном числовом выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере вычисляется `COT` указанного угла.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Результирующий набор:  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
