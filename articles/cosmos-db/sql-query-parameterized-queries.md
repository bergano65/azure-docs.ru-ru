---
title: Параметризованные запросы в Azure Cosmos DB
description: Дополнительные сведения о параметризованных запросах SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003601"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Параметризованные запросы в Azure Cosmos DB

Cosmos DB поддерживает запросы с параметрами, выраженными знаком @ Notation. Параметризованный SQL обеспечивает надежную обработку и экранирование пользовательского ввода и предотвращает случайную раскрытие данных посредством внедрения кода SQL.

## <a name="examples"></a>Примеры

Например, можно написать запрос, `lastName` принимающий и `address.state` использующий параметры, и выполнить `lastName` его для различных значений и `address.state` на основе введенных пользователем данных.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Затем этот запрос можно отправить в Cosmos DB как параметризованный запрос JSON, подобный следующему:

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

Значения параметров могут быть любым допустимым JSON: строками, числами, логическими значениями, null, даже массивами или вложенными JSON. Поскольку Cosmos DB не имеет схемы, параметры не проверяются на соответствие какому-либо типу.


## <a name="next-steps"></a>Следующие шаги

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Данные документа модели](modeling-data.md)
