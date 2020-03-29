---
title: Параметризированные запросы в Azure Cosmos DB
description: Узнайте, как параметризированные запросы S'L обеспечивают надежную обработку и избегание пользовательского ввода, а также предотвращают случайное облучение данных с помощью инъекций S'L.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870825"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Параметризированные запросы в Azure Cosmos DB

Cosmos DB поддерживает запросы с параметрами, выраженными знакомой нотации. Параметризированный СЗЛ обеспечивает надежную обработку и избегание пользовательского ввода, а также предотвращает случайное облучение данных с помощью инъекций S'L.

## <a name="examples"></a>Примеры

Например, можно написать запрос, `lastName` `address.state` который принимает и в качестве параметров, и выполнять его для различных значений `lastName` и `address.state` на основе пользовательского ввода.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Затем вы можете отправить этот запрос в Cosmos DB в качестве параметризированного запроса JSON, как следующее:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Следующий пример устанавливает аргумент TOP с парапарализированным запросом: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Значения параметра могут быть любыми действительными JSON: строки, числа, booleans, null, даже массивы или вложенные JSON. Поскольку Cosmos DB не является схемативом, параметры не проверяются против любого типа.


## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Данные документов модели](modeling-data.md)
