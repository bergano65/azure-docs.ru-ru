---
title: Присвоение псевдонимов в Azure Cosmos DB
description: Сведения о присвоении псевдонимов в запросах Azure Cosmos DB SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002070"
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

## <a name="next-steps"></a>Следующие шаги

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Предложение SELECT](sql-query-select.md)
- [Предложение FROM](sql-query-from.md)
