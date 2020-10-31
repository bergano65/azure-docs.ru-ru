---
title: Параметризованные запросы в Azure Cosmos DB
description: Узнайте о том, как параметризованные запросы SQL обеспечивают надежную обработку и экранирование вводимых пользователем данных, а затем предотвращают случайное воздействие на данные посредством внедрения кода SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: a0f354a0420521ede45740a4eb9c492089e36bda
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089225"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Параметризованные запросы в Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB поддерживает запросы с параметрами, выраженными знаком @ Notation. Параметризованный SQL обеспечивает надежную обработку и экранирование пользовательского ввода и предотвращает случайную раскрытие данных посредством внедрения кода SQL.

## <a name="examples"></a>Примеры

Например, можно написать запрос, принимающий и использующий `lastName` `address.state` Параметры, и выполнить его для различных значений `lastName` и `address.state` на основе введенных пользователем данных.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Затем этот запрос можно отправить в Azure Cosmos DB как параметризованный запрос JSON, подобный следующему:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

В следующем примере для аргумента TOP задается параметризованный запрос:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Значения параметров могут быть любым допустимым JSON: строками, числами, логическими значениями, null, даже массивами или вложенными JSON. Поскольку Azure Cosmos DB не имеет схемы, параметры не проверяются на соответствие какому-либо типу.

Ниже приведены примеры параметризованных запросов в каждом пакете SDK Azure Cosmos DB.

- [Пакет SDK для .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Данные документов модели](modeling-data.md)
