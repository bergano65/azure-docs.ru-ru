---
title: Прозвище в Azure Космос DB
description: Узнайте, как использовать псевдоним в запросах Azure Cosmos DB S'L, чтобы дифференцировать два свойства с одинаковым именем
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873477"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Прозвище в Azure Космос DB

Вы можете явно псевдоним значения в запросах. Если запрос имеет два свойства с одинаковым именем, используйте псевдоним, чтобы переименовать одно или оба свойства, чтобы они были disambiguated в прогнозируемом результате.

## <a name="examples"></a>Примеры

Ключевое слово AS, используемое для псевдонима, является необязательным, как `NameInfo`показано в следующем примере при проецирование второго значения как:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Оговорка SELECT](sql-query-select.md)
- [ИЗ ПУНКТА](sql-query-from.md)
