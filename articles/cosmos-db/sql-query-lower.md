---
title: НИЖЕ Azure Cosmos DB язык запросов
description: Сведения о более РАННЕй системной функции SQL в Azure Cosmos DB для возврата строкового выражения после преобразования символьных данных в нижний регистр
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 163b893b74b5f6d3950ae92e209f96abde8cd90e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075285"
---
# <a name="lower-azure-cosmos-db"></a>НИЖЕ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает строковое выражение после преобразования символов верхнего регистра в нижний.  

Нижняя системная функция не использует индекс. Если планируется регулярное сравнение без учета регистра, то более низкая системная функция может потреблять значительное количество единиц запросов. В этом случае вместо использования нижней системной функции для нормализации данных каждый раз при сравнении можно нормализовать регистр при вставке. Затем запрос, например SELECT * FROM c, где ниже (c. Name) = ' Bob ', просто превращается в SELECT * FROM c, где c.name = ' Bob '.

## <a name="syntax"></a>Синтаксис
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `LOWER` в запросе.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Результирующий набор:  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
