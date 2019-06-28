---
title: Совмещение имен в Azure Cosmos DB
description: Дополнительные сведения о значениях псевдонимы в запросах Azure Cosmos DB SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342520"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Совмещение имен в Azure Cosmos DB

Вы можете явно псевдоним значения в запросах. Если в запросе присутствует два свойства с тем же именем, используйте псевдонимы для переименования одного или обоих свойств, поэтому они являетесь неоднозначность в отображаемом результате.

## <a name="examples"></a>Примеры

Ключевое слово, используемое для создания псевдонимов является необязательным, как показано в следующем примере при отображении второго значения в виде `NameInfo`:

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

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Предложение SELECT](sql-query-select.md)
- [Предложение FROM](sql-query-from.md)
