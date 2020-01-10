---
title: НИЖЕ Azure Cosmos DB язык запросов
description: Сведения о более РАННЕй системной функции SQL в Azure Cosmos DB для возврата строкового выражения после преобразования символьных данных в нижний регистр
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 35efbb8d4d97ab52abb20487d15a80985946c499
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732609"
---
# <a name="lower-azure-cosmos-db"></a>НИЖЕ (Azure Cosmos DB)
 Возвращает строковое выражение после преобразования символов верхнего регистра в нижний.  

Нижняя системная функция не использует индекс. Если планируется регулярное сравнение без учета регистра, то более низкая системная функция может потреблять значительное количество единиц запросов. В этом случае вместо использования нижней системной функции для нормализации данных каждый раз при сравнении можно нормализовать регистр при вставке. Затем запрос, например SELECT * FROM c, где ниже (c. Name) = ' Bob ', просто превращается в SELECT * FROM c, где c.name = ' Bob '.

## <a name="syntax"></a>Синтаксис
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.  
  
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

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
