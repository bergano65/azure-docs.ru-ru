---
title: Предложение ЛИМИТа смещения в Azure Cosmos DB
description: Узнайте, как использовать предложение СМЕЩ LIMIT для пропуска и получения определенных значений при запросе в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: a8df220be211c3c8d8cdeab8a8aebfd35e77ebf8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732592"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Предложение ЛИМИТа смещения в Azure Cosmos DB

Предложение СМЕЩ LIMIT является необязательным предложением, которое можно пропустить, а затем взять определенное количество значений из запроса. Число СМЕЩЕНий и число ПРЕДЕЛов необходимо указать в предложении LIMIT OFFSET.

Если предельное значение смещения используется в сочетании с предложением ORDER BY, результирующий набор создается путем выполнения инструкции SKIP и получения упорядоченных значений. Если предложение ORDER BY не используется, то оно приведет к определенному порядку значений.

## <a name="syntax"></a>Синтаксис
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Аргументы

- `<offset_amount>`

   Указывает целое число элементов, которые должны пропускаться в результатах запроса.

- `<limit_amount>`
  
   Указывает целое число элементов, которые должны быть включены в результаты запроса

## <a name="remarks"></a>Remarks
  
  В предложении "предельное значение смещения" требуются как количество СМЕЩЕНий, так и число ПРЕДЕЛов. Если используется необязательное предложение `ORDER BY`, результирующий набор создается путем пропуска всех упорядоченных значений. В противном случае запрос вернет фиксированный порядок значений. Теперь это предложение поддерживается для запросов в одной секции, а также для запросов между секциями.

## <a name="examples"></a>Примеры

Например, ниже приведен запрос, который пропускает первое значение и возвращает второе значение (в порядке названия резидентного города):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Результаты:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Ниже приведен запрос, который пропускает первое значение и возвращает второе значение (без упорядочения):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Результаты:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Предложение SELECT](sql-query-select.md)
- [Предложение ORDER BY](sql-query-order-by.md)
