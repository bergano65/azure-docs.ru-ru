---
title: Присвоение псевдонимов в Azure Cosmos DB
description: Узнайте, как использовать псевдонимы в запросах SQL Azure Cosmos DB для различения двух свойств с одинаковыми именами
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873477"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Присвоение псевдонимов в Azure Cosmos DB

В запросах можно явно задавать псевдонимы значений. Если запрос содержит два свойства с одинаковым именем, используйте псевдонимы для переименования одного или обоих свойств, чтобы они были неоднозначной в прогнозируемом результате.

## <a name="examples"></a>Примеры

Ключевое слово AS, используемое для псевдонима, является необязательным, как показано в следующем примере при проецировании второго значения в `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Получаются такие результаты:

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
