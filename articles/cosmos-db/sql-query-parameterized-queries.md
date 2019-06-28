---
title: Параметризованные запросы в Azure Cosmos DB
description: Дополнительные сведения о параметризованных SQL-запросов
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342840"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Параметризованные запросы в Azure Cosmos DB

Cosmos DB поддерживает запросы с параметрами, выраженными с привычной нотации @. Параметризованный SQL обеспечивает надежную обработку и экранирование пользовательского ввода и предотвращает случайное раскрытие данных путем внедрения кода SQL.

## <a name="examples"></a>Примеры

Например, можно написать запрос, который принимает `lastName` и `address.state` как параметры и выполните его для различных значений `lastName` и `address.state` на основе ввода пользователя.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Затем можно отправить этот запрос к Cosmos DB качестве параметризованного запроса JSON следующим образом:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

В следующем примере задается аргумент TOP с параметризованным запросом: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Значения параметров могут быть любые допустимые JSON: строки, числа, логические значения, null, даже массивы или вложенные JSON. Так как Cosmos DB является бессхемным, параметры не проверяются на соответствие любого типа.


## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Данные документов модели](modeling-data.md)
