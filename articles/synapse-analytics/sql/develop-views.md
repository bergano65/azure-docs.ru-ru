---
title: Представления по Т-СЗЛ с помощью Synapse S'L
description: Советы по использованию представлений T-S'L и разработке решений с помощью Synapse S'L.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428658"
---
# <a name="t-sql-views-using-synapse-sql"></a>Представления по Т-СЗЛ с помощью Synapse S'L
В этой статье вы найдете советы по использованию представлений T-S'L и разработке решений с помощью Synapse S'L. 

## <a name="why-use-views"></a>Зачем нужны представления?

Представления можно использовать разными способами для повышения качества вашего решения.  В этой статье приведены несколько примеров того, как обогатить ваше решение с представлениями и включает в себя ограничения, которые должны быть рассмотрены.

### <a name="sql-pool---create-view"></a>Пул сs-L - создание представления

> [!NOTE]
> **В**этой статье не обсуждается объединение : Syntax для CREATE VIEW не обсуждается в этой статье. Дополнительные сведения см. в [документации по CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>S'L по требованию (предварительный просмотр) - создать представление

> [!NOTE]
> **В**этой статье не обсуждается syntax for CREATE VIEW. Дополнительные сведения см. в [документации по CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Архитектурная абстракция

Общим шаблоном приложения является повторное создание таблиц с использованием [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), за которым следует шаблон переименования объекта при загрузке данных.

Приведенный ниже пример добавляет новые записи даты в измерение даты. Обратите внимание, что сначала создается новая таблица DimDate_New, которая затем переименовывается для замены исходной версии таблицы.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Имейте в виду, что такой подход может привести к появлению и исчезновению таблиц из представления пользователя и вызовет сообщения об ошибках "таблица не существует". Представления могут использоваться для обеспечения пользователей согласованным уровнем представления, в то время как основные объекты переименовываются.

Предоставляя доступ к данным через представления, пользователям не нужна видимость базовых таблиц. В дополнение к последовательному пользовательскому опыту, этот слой гарантирует, что аналитики дизайнеры могут развивать модель данных. Возможность развивать базовые таблицы означает, что дизайнеры могут использовать CTAS для максимизации производительности в процессе загрузки данных.

## <a name="performance-optimization"></a>Оптимизация производительности

Представления также могут использоваться для обеспечения оптимизации соединений между таблицами. Например, в представление можно включить ключ избыточного распространения, заданный в критерии соединения, чтобы минимизировать перемещение данных.

Еще одним преимуществом использования представлений T-S'L является использование определенных запросов или присоединение к подсказке. Таким образом, возможности представления гарантируют, что соединения всегда выполняются оптимальным образом. Вы избежите необходимости для пользователей помнить правильную конструкцию для своих соединений.

## <a name="limitations"></a>Ограничения

Представления в Synapse S'L хранятся только в виде метаданных. Следовательно, следующие варианты недоступны:

* Нет параметра связывания схемы
* Базовые таблицы не могут обновляться через представление
* Представления не могут быть созданы через временные таблицы
* Там нет поддержки для EXPAND / NOEXPAND намеки
* Индексированных представлений в Synapse S'L нет

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке можно узнать на [примере разработки Synapse S'L.](develop-overview.md)



