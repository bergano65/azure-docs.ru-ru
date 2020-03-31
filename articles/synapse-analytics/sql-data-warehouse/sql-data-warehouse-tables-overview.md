---
title: Проектирование таблиц
description: Общие сведения о проектировании таблиц в хранилище данных SQL Azure.
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
ms.openlocfilehash: f116897bdaffa765404aa47fda4ae32a49fa99ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351265"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Проектирование таблиц в хранилище данных SQL Azure

Ознакомьтесь с ключевыми понятиями о проектировании таблиц в хранилище данных SQL Azure. 

## <a name="determine-table-category"></a>Определение категории таблицы 

При [схеме типа "звезда"](https://en.wikipedia.org/wiki/Star_schema) данные упорядочиваются в таблицы фактов и измерений. Некоторые таблицы используются для интеграции или хранения данных до того, как они перейдут в таблицу фактов или измерений. При конструировании таблицы решите, к какой таблице относятся данные: фактов, измерений или интеграции. Это решение влияет на соответствующую структуру и распределение таблицы. 

- **Таблицы фактов** содержат количественные данные, которые обычно генерируются в транзакционной системе, а затем загружаются в хранилище данных. Например, розничное предприятие ежедневно создает транзакции продаж, а затем загружает данные в таблицу фактов хранилища данных для анализа.

- **Таблицы измерений** содержат данные атрибутов, которые могут измениться, хотя обычно это происходит редко. Например, имя и адрес клиента хранятся в таблице измерений и обновляются только при изменении профиля клиента. Чтобы свести к минимуму размер большой таблицы фактов, необязательно включать имя и адрес клиента в каждой строке. Вместо этого в таблице фактов и таблице измерений может совместно использоваться идентификатор клиента. Запрос может объединить две таблицы, чтобы связать профиль и транзакции клиента. 

- **Таблицы интеграции** служат для интеграции или промежуточного размещения данных. Можно создать таблицу интеграции в виде обычной таблицы, внешней таблицы или временной таблицы. Например, вы можете загружать данные в промежуточную таблицу, выполнять преобразования данных в режиме промежуточного размещения, а затем вставлять их в рабочую таблицу.

## <a name="schema-and-table-names"></a>Имена схем и таблиц
Схемы являются хорошим способом группы таблиц, используемых в аналогичной манере, вместе.  Если вы мигрируете с несколькими базами данных из предварительного решения в хранилище данных S'L, лучше всего перенести все таблицы факта, измерения и интеграции в одну схему в Хранилище данных S'L. Например, можно хранить все таблицы в хранилище данных [образеца WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) в одной схеме, называемой wwi. Следующий код создает [схему, определяемую пользователем,](/sql/t-sql/statements/create-schema-transact-sql) называемую wwi.

```sql
CREATE SCHEMA wwi;
```

Чтобы показать организацию таблиц в хранилище данных SQL, можно использовать в качестве префиксов имен таблиц значения fact, dim и int. В следующей таблице показаны некоторые имена схем и таблиц для WideWorldImportersDW.  

| Таблица WideWorldImportersDW  | Тип таблицы | Хранилище данных SQL |
|:-----|:-----|:------|:-----|
| Город | Измерение | wwi.DimCity |
| Порядок | Факты | wwi.FactOrder |


## <a name="table-persistence"></a>Сохраняемость таблицы 

Данные таблиц хранятся на постоянной основе в службе хранилища Azure, временно — в службе хранилища Azure или в хранилище данных, являющемся внешним по отношению к хранилищу данных.

### <a name="regular-table"></a>Обычная таблица

Данные обычной таблицы хранятся в службе хранилища Azure как часть хранилища данных. Таблица и данные сохраняются независимо от того, открыт ли сеанс.  В этом примере создается обычная таблица с двумя столбцами. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Временная таблица
Временная таблица существует, только пока отрыт сеанс. Можно использовать временную таблицу, чтобы другие пользователи не видели временных результатов, а также уменьшить необходимость очистки.  Временные таблицы используют локальное хранилище, чтобы обеспечить быструю производительность.  Дополнительные сведения см. в статье [Временные таблицы в хранилище данных SQL](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Внешняя таблица
Внешняя таблица указывает на данные, расположенные в большом двоичном объекте службы хранилища Azure или Azure Data Lake Store. При использовании в сочетании с инструкцией CREATE TABLE AS SELECT данные, выбранные из внешней таблицы, импортируются в хранилище данных SQL. Таким образом внешние таблицы можно использовать для загрузки данных. Руководство по загрузке см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с помощью PolyBase](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Типы данных
Хранилище данных SQL поддерживает самые распространенные типы данных. Список поддерживаемых типов данных представлен в [справочнике по типах данных в инструкции CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes). Рекомендации по использованию типов данных см. в статье [Руководство по определению типов данных для таблиц в хранилище данных SQL](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Распределенные таблицы
Фундаментальной особенностью хранилища данных S'L является способ хранения и работы на столах между [дистрибутивами.](massively-parallel-processing-mpp-architecture.md#distributions)  Хранилище данных s'L поддерживает три метода для распространения данных, кругового (по умолчанию), хэша и реплицирования.

### <a name="hash-distributed-tables"></a>Таблицы с хэш-распределением
Распределенная таблица хэша распределяет строки на основе значения в столбце распределения. Таблица распределения хэша предназначена для достижения высокой производительности запросов на больших таблицах. При выборе столбца распределения необходимо учитывать несколько факторов. 

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Реплицированные таблицы
Копия реплицированной таблицы хранится на каждом вычислительном узле. Запросы в реплицированных таблицах выполняются быстро, поскольку объединения в них не требуют перемещения данных. Тем не менее репликация требует дополнительного места и она не целесообразна для больших таблиц. 

Дополнительные сведения см. в статье [Руководство по проектированию для использования реплицированных таблиц в хранилище данных SQL Azure](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Таблицы с распределением методом циклического перебора
Строки таблицы с распределением методом циклического перебора распределяются равномерно по всем распределениям. Строки распределяются случайным образом. Загрузка данных в таблицу с распределением методом циклического перебора происходит быстро.  Однако запросы могут требовать больше перемещений данных, чем при других методах распределения. 

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Общие методы распределения для таблиц
Вариант распределения таблицы часто зависит от категории таблицы. 

| Категории таблицы | Рекомендуемый вариант распределения |
|:---------------|:--------------------|
| Факты           | Используйте хэш-распределение с кластеризованным индексом columnstore. Производительность улучшается, когда две хэш-таблицы объединены по одному столбцу распределения. |
| Измерение      | Используйте репликацию для небольших таблиц. Если таблицы слишком велики для хранения на каждом вычислительном узле, используйте хэш-распределение. |
| Промежуточная        | Используйте циклический перебор для промежуточных таблиц. Загрузка с помощью инструкции CTAS выполняется быстро. Как только данные поставляются в таблице, используйте INSERT... SELECT для перемещения данных в производственные таблицы. |

## <a name="table-partitions"></a>Разделы таблицы
Секционированная таблица хранит данные и выполняет операции со строками таблицы в соответствии с диапазонами данных. Например, таблицу можно разделить по дню, месяцу или году. Вы можете улучшить производительность запросов путем исключения секций, что ограничивает проверку запросов к данным в секции. Вы также можете управлять данными путем переключения разделов. Так как данные в хранилище данных SQL уже распределены, слишком большое количество разделов может замедлить производительность запросов. Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL](sql-data-warehouse-tables-partition.md).  При переключении раздела в непустые разделы, рассмотрите возможность использования TRUNCATE_TARGET опции в выписке [ALTER TABLE,](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) если существующие данные должны быть усечены. Ниже приведенный код переключается в преобразованных ежедневных данных в SalesFact, перезаписывая любые существующие данные. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Индексы columnstore
По умолчанию в хранилище данных SQL таблицы хранятся в виде кластеризованных индексов columnstore. Такая форма хранения данных обеспечивает высокое сжатие данных и производительность запросов в больших таблицах.  Кластеризованный индекс columnstore обычно является лучшим выбором, но в некоторых случаях подходящей структурой хранения являются кластеризованный индекс или куча.  Таблица кучи может быть особенно полезна для загрузки переходных данных, таких как таблица, которая преобразуется в финальный стол.

Список функций индексов columnstore см. в статье [Новые возможности индексов columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Сведения о повышении производительности индекса columnstore см. в статье [Максимальное повышение качества группы строк для индекса columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Статистика
Оптимизатор запросов использует статистику уровня столбца при создании плана выполнения запроса. Для повышения производительности запроса важно иметь статистику по отдельным столбцам, особенно столбцов, используемым в соединениях запросов. [Создание статистики](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) происходит автоматически.  Однако обновление статистики не происходит автоматически. Обновите статистику после добавления или изменения значительного числа строк. Например, обновите статистику после загрузки. Дополнительные сведения см. в статье [Управление статистикой таблиц в хранилище данных SQL](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Первичный ключ и уникальный ключ
PRIMARY KEY поддерживается только тогда, когда используются NONCLUSTERED и NOT ENFORCED.  Ограничение УНИКУЕ поддерживается только с помощью NOT ENFORCED используется.  Проверяйте [ограничения на хранение данных в таблице S'L.](sql-data-warehouse-table-constraints.md)

## <a name="commands-for-creating-tables"></a>Команды для создания таблиц
Вы можете создать пустую таблицу. Вы также можете создать и заполнить таблицу результатами инструкции Select. Ниже приведены команды T-SQL для создания таблицы.

| Инструкция Т-SQL | Описание |
|:----------------|:------------|
| [СОЗДАТЬ ТАБЛИЦУ](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Создает пустую таблицу, определив все столбцы и параметры таблицы. |
| [СОЗДАНИЕ ВНЕШНЕЙ ТАБЛИЦЫ](/sql/t-sql/statements/create-external-table-transact-sql) | Создает внешнюю таблицу. Определение таблицы хранится в хранилище данных SQL. Данные таблицы хранятся в хранилище BLOB-объектов Azure или в Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Задает новую таблицу с результатами инструкции Select. Столбцы и типы данных таблицы основаны на результатах инструкции Select. Чтобы импортировать данные, эта инструкция может выбрать данные из внешней таблицы. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Создает новую внешнюю таблицу, экспортируя результаты инструкции Select во внешнее расположение.  Расположением является либо хранилище BLOB-объектов Azure, либо Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Согласование исходных данных с хранилищем данных

Таблицы хранилища данных заполняются путем загрузки данных из другого источника данных. Для успешной загрузки число и типы данных столбцов исходных данных должны совпадать с определением таблицы в хранилище данных. Согласование данных может оказаться самой сложной частью разработки таблиц. 

Если данные поступают из нескольких хранилищ данных, вы можете поместить их в хранилище данных и сохранить их в таблице интеграции. Когда данные находятся в таблице интеграции, вы можете использовать возможности хранилища данных SQL для выполнения операций преобразования. После подготовки данных их можно вставить в рабочие таблицы.

## <a name="unsupported-table-features"></a>Неподдерживаемые функции таблиц
Хранилище данных SQL поддерживает многие, но не все функции таблицы, предлагаемые другими базами данных.  В следующем списке содержатся некоторые функции таблицы, которые не поддерживаются в хранилище данных SQL.

- Иностранный ключ, [Ограничения таблицы](/sql/t-sql/statements/alter-table-table-constraint-transact-sql) проверки
- [Вычисленные столбцы](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Индексированные представления](/sql/relational-databases/views/create-indexed-views)
- [Последовательности](/sql/t-sql/statements/create-sequence-transact-sql)
- [Разрежены колонны](/sql/relational-databases/tables/use-sparse-columns)
- Суррогатные ключи (реализация посредством [удостоверения](sql-data-warehouse-tables-identity.md));
- [Синонимы](/sql/t-sql/statements/create-synonym-transact-sql)
- [Триггеры](/sql/t-sql/statements/create-trigger-transact-sql)
- [Уникальные индексы](/sql/t-sql/statements/create-index-transact-sql)
- [Пользовательские типы](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Запросы размера таблицы
Простой способ определить пространство и строки, используемые таблицей в каждом из 60 распределений, — применить инструкцию [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Однако возможности команд DBCC достаточно ограничены.  Динамические административные представления (DMV) отображают больше сведений, чем команды DBCC. Начните с создания этого представления.

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

Этот запрос возвращает количество строк и объем каждой таблицы.  Оно позволяет узнать, какие таблицы занимают большего всего места, в каких таблицах применяется циклический перебор, какие таблицы реплицированы, а в каких используется хэш-распределение.  Для таблиц с хэш-распределением запрос показывает столбец распределения.  

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
После создания таблицы для хранилища данных загрузите данные в таблицу.  Инструкции см. в статье о [загрузке данных в хранилище данных SQL](load-data-wideworldimportersdw.md).
