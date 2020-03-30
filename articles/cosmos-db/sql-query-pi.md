---
title: PI на языке запросов Azure Cosmos DB
description: Узнайте о функции системы ИП в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349650"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Космос DB)
 Возвращает константное значение PI.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример возвращает значение `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Результирующий набор:  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
