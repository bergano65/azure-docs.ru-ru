---
title: Удаление данных из обозревателя данных Azure
description: В этой статье описаны сценарии удаления в Azure Data Explorer, включая чистку, удаление степени и удаление на основе удержания.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501409"
---
# <a name="delete-data-from-azure-data-explorer"></a>Удаление данных из обозревателя данных Azure

Azure Data Explorer поддерживает различные сценарии удаления, описанные в этой статье. 

## <a name="delete-data-using-the-retention-policy"></a>Удаление данных с помощью политики удержания

Azure Data Explorer автоматически удаляет данные на основе [политики удержания.](/azure/kusto/management/retentionpolicy) Этот метод является наиболее эффективным и беспереданный способом удаляния данных. Установите политику удержания на уровне базы данных или таблицы.

Рассмотрим базу данных или таблицу со сроком хранения в 90 дней. Если требуется всего 60 дней данных, удалите старые данные следующим образом:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Удаление данных путем удаления степени

[Extent (осколок данных)](/azure/kusto/management/extents-overview) — это внутренняя структура, в которой хранятся данные. Каждая степень может вместить до миллионов записей. Масштабы могут быть удалены индивидуально или как группа с использованием [команд drop extent (ы)](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Примеры

Вы можете удалить все строки в таблице или только в определенной степени.

* Удалите все строки в таблице:

    ```kusto
    .drop extents from TestTable
    ```

* Удалить определенный упор:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Удаление отдельных строк с помощью чистки

[Очистка данных](/azure/kusto/concepts/data-purge) может быть использована для удаляния строк отдельных лиц. Удаление не является немедленным и требует значительных системных ресурсов. Таким образом, рекомендуется только для сценариев соответствия.  

