---
title: Присвоение псевдонимов в Azure Cosmos DB
description: Узнайте, как использовать псевдонимы в запросах SQL Azure Cosmos DB для различения двух свойств с одинаковыми именами
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873477"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Присвоение псевдонимов в Azure Cosmos DB

В запросах можно явно задавать псевдонимы значений. Если запрос содержит два свойства с одинаковым именем, используйте псевдонимы для переименования одного или обоих свойств, чтобы они были неоднозначной в прогнозируемом результате.

## <a name="examples"></a>Примеры

Ключевое слово AS, используемое для псевдонима, является необязательным, как показано в следующем примере при проецировании `NameInfo`второго значения на:

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
- [Предложение SELECT](sql-query-select.md)
- [Предложение FROM](sql-query-from.md)
