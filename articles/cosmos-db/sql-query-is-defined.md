---
title: IS_DEFINED на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System IS_DEFINED в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349872"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Возвращает логическое значение, указывающее, назначено ли свойству значение.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Любое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже проверяется наличие свойства в указанном документе JSON. Первый пример возвращает значение true, так как присутствует значение "a", но второй возвращает значение false, так как значение "b" отсутствует.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Результирующий набор:  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Функции проверки типов Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
