---
title: Используйте метки запросов в Synapse S'L
description: В эту статью включены важные советы по использованию меток запросов в Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430036"
---
# <a name="use-query-labels-in-synapse-sql"></a>Используйте метки запросов в Synapse S'L
В эту статью включены важные советы по использованию меток запросов в Synapse S'L.

> [!NOTE]
> По требованию (предварительный просмотр) S'L не поддерживает запросы на маркировку.

## <a name="what-are-query-labels"></a>Что такое метки запросов
Пул сsL поддерживает концепцию, называемую метками запросов. Прежде чем углубляться в это понятие, рассмотрим пример.

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Последняя строка добавляет к запросу тег "Метка моего запроса". Это удобно, поскольку метки можно использовать в запросах в динамических административных представлениях. Запрос для меток обеспечивает механизм поиска проблемных запросов и помогает определить прогресс с помощью выполнения ELT.

Хорошая конвенция именования наиболее полезна. Например, запуск метки с PROJECT, PROCEDURE, STATEMENT или COMMENT однозначно идентифицирует запрос между всем кодом в исходном управлении.

В следующем запросе используется динамическое представление управления для поиска по метке:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Слово label в запросе необходимо заключать в квадратные скобки или двойные кавычки. Label — зарезервированное слово. Его использование без разделителя приводит к ошибке. 
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](develop-overview.md).


