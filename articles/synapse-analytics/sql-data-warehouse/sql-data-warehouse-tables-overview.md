---
title: Проектирование таблиц
description: Введение в проектирование таблиц в бассейне Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c5964bc944cd50e05d548eb731450a4944e854d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631263"
---
# <a name="design-tables-in-synapse-sql-pool"></a>Таблицы дизайна в бассейне Synapse S'L

В этой статье представлены ключевые вводные концепции для проектирования таблиц в пуле S'L.

## <a name="determine-table-category"></a>Определение категории таблицы

При [схеме типа "звезда"](https://en.wikipedia.org/wiki/Star_schema) данные упорядочиваются в таблицы фактов и измерений. Некоторые таблицы используются для интеграции или хранения данных до того, как они перейдут в таблицу фактов или измерений. При конструировании таблицы решите, к какой таблице относятся данные: фактов, измерений или интеграции. Это решение влияет на соответствующую структуру и распределение таблицы.

- **Таблицы фактов** содержат количественные данные, которые обычно генерируются в транзакционной системе, а затем загружаются в пул S'L. Например, розничный бизнес генерирует транзакции продаж каждый день, а затем загружает данные в таблицу фактов пула S'L для анализа.

- **Таблицы измерений** содержат данные атрибутов, которые могут измениться, хотя обычно это происходит редко. Например, имя и адрес клиента хранятся в таблице измерений и обновляются только при изменении профиля клиента. Чтобы свести к минимуму размер большой таблицы фактов, имя и адрес клиента не должны быть в каждом ряду таблицы фактов. Вместо этого в таблице фактов и таблице измерений может совместно использоваться идентификатор клиента. Запрос может объединить две таблицы, чтобы связать профиль и транзакции клиента.

- **Таблицы интеграции** служат для интеграции или промежуточного размещения данных. Можно создать таблицу интеграции в виде обычной таблицы, внешней таблицы или временной таблицы. Например, вы можете загружать данные в промежуточную таблицу, выполнять преобразования данных в режиме промежуточного размещения, а затем вставлять их в рабочую таблицу.

## <a name="schema-and-table-names"></a>Имена схем и таблиц

Схемы являются хорошим способом группы таблиц, используемых в аналогичной манере, вместе.  Если вы мигрируете с несколькими базами данных из решения on-prem в пул S'L, лучше всего перенести все таблицы факта, измерения и интеграции в одну схему в пуле S'L.

Например, все таблицы можно хранить в пуле [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) образца S'L в рамках одной схемы, называемой wwi. Следующий код создает [схему, определяемую пользователем,](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) называемую wwi.

```sql
CREATE SCHEMA wwi;
```

Чтобы показать организацию таблиц в пуле S'L, можно использовать факты, тусклые и int в качестве префиксов к именам таблиц. В следующей таблице показаны некоторые имена схем и таблиц для WideWorldImportersDW.  

| Таблица WideWorldImportersDW  | Тип таблицы | Бассейн СЗЛ |
|:-----|:-----|:------|:-----|
| Город | Измерение | wwi.DimCity |
| Порядок | Факты | wwi.FactOrder |

## <a name="table-persistence"></a>Сохраняемость таблицы

Таблицы хранят данные либо постоянно в Хранилище Azure, временно в Хранилище Azure, либо в хранилище данных, внешнем пуле S'L.

### <a name="regular-table"></a>Обычная таблица

Обычная таблица хранит данные в Azure Storage как часть пула S'L. Таблица и данные сохраняются независимо от того, открыт ли сеанс.  Следующий пример создает обычную таблицу с двумя столбцов.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Временная таблица

Временная таблица существует, только пока отрыт сеанс. Можно использовать временную таблицу, чтобы другие пользователи не видели временных результатов, а также уменьшить необходимость очистки.  

Временные таблицы используют локальное хранилище, чтобы обеспечить быструю производительность.  Дополнительные сведения см. в статье [Временные таблицы в хранилище данных SQL](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Внешняя таблица

Внешняя таблица указывает на данные, расположенные в большом двоичном объекте службы хранилища Azure или Azure Data Lake Store. При использовании в сочетании с заявлением CREATE TABLE AS SELECT выберите из внешней таблицы импорт данных в пул S'L.

Таким образом, внешние таблицы полезны для загрузки данных. Руководство по загрузке см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с помощью PolyBase](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Типы данных

Пулс S'L поддерживает наиболее часто используемые типы данных. Список поддерживаемых типов данных представлен в [справочнике по типах данных в инструкции CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes). Рекомендации по использованию типов данных см. в статье [Руководство по определению типов данных для таблиц в хранилище данных SQL](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Распределенные таблицы

Фундаментальной особенностью пула S'L является способ его хранения и работы на столах между [дистрибутивами.](massively-parallel-processing-mpp-architecture.md#distributions)  Пул s'L поддерживает три метода распространения данных: круговой (по умолчанию), хэш и реплицируется.

### <a name="hash-distributed-tables"></a>Таблицы с хэш-распределением

Распределенная таблица хэша распределяет строки на основе значения в столбце распределения. Таблица распределения хэша предназначена для достижения высокой производительности запросов на больших таблицах. При выборе столбца распределения необходимо учитывать несколько факторов.

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Реплицированные таблицы

Копия реплицированной таблицы хранится на каждом вычислительном узле. Запросы быстро работают на реплицируемых таблицах, так как соединения на реплицированных таблицах не требуют движения данных. Репликация требует дополнительного хранения, хотя и не практично для больших таблиц.

Дополнительные сведения см. в статье [Руководство по проектированию для использования реплицированных таблиц в хранилище данных SQL Azure](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Таблицы с распределением методом циклического перебора

Строки таблицы с распределением методом циклического перебора распределяются равномерно по всем распределениям. Строки распределяются случайным образом. Загрузка данных в таблицу с распределением методом циклического перебора происходит быстро.  Имейте в виду, что запросы могут потребовать большего количества передачи данных, чем другие методы распределения.

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Общие методы распределения для таблиц

Вариант распределения таблицы часто зависит от категории таблицы.

| Категории таблицы | Рекомендуемый вариант распределения |
|:---------------|:--------------------|
| Факты           | Используйте хэш-распределение с кластеризованным индексом columnstore. Производительность улучшается, когда две хэш-таблицы объединены по одному столбцу распределения. |
| Измерение      | Используйте репликацию для небольших таблиц. Если таблицы слишком велики для хранения на каждом вычислительном узле, используйте хэш-распределение. |
| Промежуточная        | Используйте циклический перебор для промежуточных таблиц. Загрузка с помощью инструкции CTAS выполняется быстро. Как только данные поставляются в таблице, используйте INSERT... SELECT для перемещения данных в производственные таблицы. |

## <a name="table-partitions"></a>Разделы таблицы

Секционированная таблица хранит данные и выполняет операции со строками таблицы в соответствии с диапазонами данных. Например, таблицу можно разделить по дню, месяцу или году. Вы можете улучшить производительность запросов путем исключения секций, что ограничивает проверку запросов к данным в секции. Вы также можете управлять данными путем переключения разделов. Так как данные в хранилище данных SQL уже распределены, слишком большое количество разделов может замедлить производительность запросов. Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL](sql-data-warehouse-tables-partition.md).  При переключении раздела в непустые разделы, рассмотрите возможность использования TRUNCATE_TARGET опции в выписке [ALTER TABLE,](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) если существующие данные должны быть усечены. Ниже приведенный код переключается в преобразованных ежедневных данных в SalesFact, перезаписывая любые существующие данные.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Индексы columnstore

По умолчанию пул s'L хранит таблицу в виде кластерного индекса столбцов. Такая форма хранения данных обеспечивает высокое сжатие данных и производительность запросов в больших таблицах.  

Кластеризованный индекс columnstore обычно является лучшим выбором, но в некоторых случаях подходящей структурой хранения являются кластеризованный индекс или куча.  

> [!TIP]
> Таблица кучи может быть особенно полезна для загрузки переходных данных, таких как таблица, которая преобразуется в финальный стол.

Список функций индексов columnstore см. в статье [Новые возможности индексов columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Сведения о повышении производительности индекса columnstore см. в статье [Максимальное повышение качества группы строк для индекса columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Статистика

Оптимизатор запросов использует статистику уровня столбца при создании плана выполнения запроса.

Для повышения производительности запроса важно иметь статистику по отдельным столбцам, особенно столбцов, используемым в соединениях запросов. [Создание статистики](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) происходит автоматически.  

Обновление статистики не происходит автоматически. Обновите статистику после добавления или изменения значительного числа строк. Например, обновите статистику после загрузки. Дополнительные сведения см. в статье [Управление статистикой таблиц в хранилище данных SQL](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Первичный ключ и уникальный ключ

PRIMARY KEY поддерживается только тогда, когда используются NONCLUSTERED и NOT ENFORCED.  Ограничение УНИКУЕ поддерживается только с помощью NOT ENFORCED используется.  Проверки [ограничений на таблицу пула S'L.](sql-data-warehouse-table-constraints.md)

## <a name="commands-for-creating-tables"></a>Команды для создания таблиц

Вы можете создать пустую таблицу. Вы также можете создать и заполнить таблицу результатами инструкции Select. Ниже приведены команды T-SQL для создания таблицы.

| Инструкция Т-SQL | Описание |
|:----------------|:------------|
| [СОЗДАТЬ ТАБЛИЦУ](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Создает пустую таблицу, определив все столбцы и параметры таблицы. |
| [СОЗДАНИЕ ВНЕШНЕЙ ТАБЛИЦЫ](/sql/t-sql/statements/create-external-table-transact-sql) | Создает внешнюю таблицу. Определение таблицы хранится в пуле S'L. Данные таблицы хранятся в хранилище BLOB-объектов Azure или в Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Задает новую таблицу с результатами инструкции Select. Столбцы и типы данных таблицы основаны на результатах инструкции Select. Чтобы импортировать данные, эта инструкция может выбрать данные из внешней таблицы. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Создает новую внешнюю таблицу, экспортируя результаты инструкции Select во внешнее расположение.  Расположением является либо хранилище BLOB-объектов Azure, либо Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-sql-pool"></a>Выравнивание исходных данных с пулом S'L

Таблицы бассейнов S'L заполняются за счет загрузки данных из другого источника данных. Для успешной загрузки число и типы данных столбцов в исходных данных должны соответствовать определению таблицы в пуле S'L. Согласование данных может оказаться самой сложной частью разработки таблиц.

Если данные поступают из нескольких хранилики данных, вы загружаете данные в пул S'L и храните их в таблице интеграции. После того, как данные находятся в таблице интеграции, можно использовать возможность пула S'L для выполнения операций преобразования. После подготовки данных их можно вставить в рабочие таблицы.

## <a name="unsupported-table-features"></a>Неподдерживаемые функции таблиц

Пул s'L поддерживает многие, но не все функции таблицы, предлагаемые другими базами данных.  В следующем списке показаны некоторые функции таблицы, которые не поддерживаются в пуле S'L:

- Иностранный ключ, [Ограничения таблицы](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) проверки
- [Вычисленные столбцы](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Индексированные представления](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Последовательности](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Разрежены колонны](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Суррогатные ключи (реализация посредством [удостоверения](sql-data-warehouse-tables-identity.md));
- [Синонимы](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Триггеры](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Уникальные индексы](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Пользовательские типы](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Запросы размера таблицы

Простой способ определить пространство и строки, используемые таблицей в каждом из 60 распределений, — применить инструкцию [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Однако возможности команд DBCC достаточно ограничены.  Динамические административные представления (DMV) отображают больше сведений, чем команды DBCC. Начните с создания этого представления:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Сводка табличного пространства

Этот запрос возвращает количество строк и объем каждой таблицы.  Это позволяет увидеть, какие таблицы являются самыми большими столами и являются ли они круговой, реплицируется или распределены хэш-распределены.  Для таблиц с хэш-распределением запрос показывает столбец распределения.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Табличное пространство по типу распределения

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Табличное пространство по типу индекса

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Сводка пространства распределения

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Дальнейшие действия

После создания таблиц для пула S'L следующим шагом является загрузка данных в таблицу.  Для руководства по [Loading data to SQL pool](load-data-wideworldimportersdw.md)загрузке см.
