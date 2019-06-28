---
title: Предложение ORDER BY в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL ORDER BY для Azure Cosmos DB. Использует SQL в качестве языка запросов Azure Cosmos DB JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342942"
---
# <a name="order-by-clause"></a>Предложение ORDER BY

Необязательное предложение ORDER BY задает порядок сортировки результатов, возвращаемых запросом.

## <a name="syntax"></a>Синтаксис
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Аргументы
  
- `<sort_specification>`  
  
   Указывает свойство или выражение, по которому производится сортировка результирующего набора запроса. Столбец сортировки можно указать как имя или свойство псевдоним.  
  
   Можно указать несколько свойств. Имена свойств должны быть уникальными. Последовательность параметров сортировки в предложении ORDER BY определяет организацию упорядоченного результирующего набора. То есть результирующий набор сортируется по первому свойству, а затем упорядоченный список сортируется по второму свойству и т. д.  
  
   Имена свойств, на которые ссылается предложение ORDER BY должны соответствовать к свойству в списке выбора или свойство, определенное в коллекции, указанной в предложении FROM.  
  
- `<sort_expression>`  
  
   Указывает один или несколько свойств, или выражения, по которому производится сортировка результирующего набора запроса.  
  
- `<scalar_expression>`  
  
   Дополнительные сведения см. в разделе [Скалярные выражения](sql-query-scalar-expressions.md).  
  
- `ASC | DESC`  
  
   Указывает порядок сортировки значений в указанном столбце (по возрастанию или по убыванию). Если указать ASC, значения сортируются по возрастанию, а если DESC — по убыванию. По умолчанию значения сортируются по возрастанию. Значения Null рассматриваются как минимальные возможные значения.  
  
## <a name="remarks"></a>Примечания  
  
   Предложение ORDER BY требует, политику индексирования индекс для поля сортировки. Среда выполнения запросов Azure Cosmos DB поддерживает сортировку с именем свойства, а не по вычисляемым свойствам. Azure Cosmos DB поддерживает несколько свойств ORDER BY. Чтобы выполнить запрос с несколькими свойствами ORDER BY, следует определить [составной индекс](index-policy.md#composite-indexes) для сортируемых полей.

## <a name="examples"></a>Примеры

Например ниже приведен запрос, возвращающий семьи по возрастанию по городу проживания.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Получаются такие результаты:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Следующий запрос получает семейство `id`s в порядке их даты создания элемента. Элемент `creationDate` представляет число *виде времени эпохи*, или прошло времени с 1 января 1970 года в секундах.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Получаются такие результаты:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Кроме того можно сортировать по нескольким свойствам. Требуется запрос, который упорядочивает элементы по нескольким свойствам [составной индекс](index-policy.md#composite-indexes). Рассмотрим следующий запрос:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Этот запрос извлекает семейства `id` по возрастанию название города. Если несколько элементов же название города, запрос будет Упорядочить по `creationDate` в порядке убывания.

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Предложение SELECT](sql-query-select.md)
- [Предложение LIMIT СМЕЩЕНИЯ](sql-query-offset-limit.md)
