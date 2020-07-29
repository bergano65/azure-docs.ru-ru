---
title: ALTER EXTERNAL STREAM (Transact-SQL) — SQL Azure для пограничных вычислений (предварительная версия)
description: Сведения об инструкции ALTER EXTERNAL STREAM в SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235197"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

Изменяет определение внешнего потока. Не допускается изменение внешнего потока, который используется заданием потоковой передачи в состоянии *Running* (Выполняется). 



## <a name="syntax"></a>Синтаксис

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Аргументы

Дополнительные сведения об аргументах команды Alter External Stream см. в статье [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Значения кода возврата

При успешном выполнении ALTER EXTERNAL STREAM возвращает 0. Если возвращается значение, отличное от нуля, значит произошел сбой.


## <a name="see-also"></a>См. также раздел

- [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 
