---
title: sys.external_job_streams (Transact-SQL) — SQL Azure для пограничных вычислений (предварительная версия)
description: Узнайте о том, как использовать sys.external_job_streams в SQL Azure для пограничных вычислений (предварительная версия).
keywords: sys.external_job_streams, SQL для пограничных вычислений
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7f26c87c0de09618b2d24413f7ff692fd764b4cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594483"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Возвращает строку для каждого входного или выходного объекта внешнего потока, сопоставленного с заданием внешней потоковой передачи.

|Имя столбца|Тип данных|Описание|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Идентификационный номер для объекта задания потоковой передачи. Этот столбец сопоставляется со столбцом object_id для sys.external_streaming_jobs.|
|**stream_id**|**int**| Идентификационный номер объекта потока. Этот столбец сопоставляется со столбцом object_id для sys.external_streams. |
|**is_input**|**bit**| Значение 1, если объект потока использует входные данные для задания потоковой передачи. В противном случае — 0.|
|**is_output**|**bit**| Значение 1, если объект потока использует выходные данные для задания потоковой передачи. В противном случае — 0.|

## <a name="example"></a>Пример

Это представление каталога используется с представлениями каталога `sys.external_streams` и `sys.external_streaming_jobs`. Результат выполнения запроса показан ниже.

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Разрешения

Видимость метаданных в представлениях каталогов ограничивается защищаемыми объектами, которыми пользователь владеет или на которые ему были предоставлены разрешения. Дополнительные сведения см. в разделе [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>См. также раздел

- [Представления каталога (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Системные представления (Transact-SQL)](/sql/t-sql/language-reference/)
