---
title: Таблицы проектирования с использованием Synapse S'L
description: Введение в проектирование таблиц в Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431882"
---
# <a name="design-tables-using-synapse-sql"></a>Таблицы проектирования с использованием Synapse S'L

Этот документ включает в себя ключевые концепции для проектирования таблиц с пулом S'L и S'L по требованию (предварительный просмотр).  

[S'L по требованию (предварительный просмотр)](on-demand-workspace-overview.md) — это служба запросов по данным в озере данных. Он не имеет локального хранения данных для приема данных. [Пул спомощьи](best-practices-sql-pool.md) представляет собой набор аналитических ресурсов, которые готовятся при использовании Synapse S'L. Размер пула SQL определяется единицами хранения данных (DWU).

В следующей таблице перечислены темы, имеющие отношение к пулу S'L по сравнению с S'L по требованию:

| Раздел                                                        | Бассейн СЗЛ | СЗЛ по запросу |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Определение категории таблицы](#determine-table-category)        | Да                | нет                      |
| [имена схем;](#schema-names)                                | Да                | Да                     |
| [Имена таблиц](#table-names)                                  | Да                | нет                      |
| [Сохраняемость таблицы](#table-persistence)                      | Да                | нет                      |
| [Обычная таблица](#regular-table)                              | Да                | нет                      |
| [Временная таблица](#temporary-table)                          | Да                | Да                     |
| [Внешняя таблица](#external-table)                            | Да                | Да                     |
| [Типы данных](#data-types)                                    | Да                | Да                     |
| [Распределенные таблицы](#distributed-tables)                    | Да                | нет                      |
| [Таблицы с хэш-распределением](#hash-distributed-tables)          | Да                | нет                      |
| [Реплицированные таблицы](#replicated-tables)                      | Да                | нет                      |
| [Таблицы с распределением методом циклического перебора](#round-robin-tables)                    | Да                | нет                      |
| [Общие методы распределения для таблиц](#common-distribution-methods-for-tables) | Да                | нет                      |
| [Секции](#partitions)                                    | Да                | Да                     |
| [Индексы columnstore](#columnstore-indexes)                  | Да                | нет                      |
| [Статистика](#statistics)                                    | Да                | Да                     |
| [Первичный ключ и уникальный ключ](#primary-key-and-unique-key)    | Да                | нет                      |
| [Команды для создания таблиц](#commands-for-creating-tables) | Да                | нет                      |
| [Согласование исходных данных с хранилищем данных](#aligning-source-data-with-the-data-warehouse) | Да                | нет                      |
| [Неподдерживаемые функции таблиц](#unsupported-table-features)    | Да                | нет                      |
| [Запросы размера таблицы](#table-size-queries)                    | Да                | нет                      |

## <a name="determine-table-category"></a>Определение категории таблицы

При [схеме типа "звезда"](https://en.wikipedia.org/wiki/Star_schema) данные упорядочиваются в таблицы фактов и измерений. Некоторые таблицы используются для интеграции или постановки данных перед переходом к таблице фактов или измерений. При конструировании таблицы решите, к какой таблице относятся данные: фактов, измерений или интеграции. Это решение влияет на соответствующую структуру и распределение таблицы.

- **Таблицы фактов** содержат количественные данные, которые обычно генерируются в транзакционной системе, а затем загружаются в хранилище данных. Например, розничное предприятие ежедневно создает транзакции продаж, а затем загружает данные в таблицу фактов хранилища данных для анализа.

- **Таблицы измерений** содержат данные атрибутов, которые могут измениться, хотя обычно это происходит редко. Например, имя и адрес клиента хранятся в таблице измерений и обновляются только при изменении профиля клиента. Чтобы свести к минимуму размер большой таблицы фактов, имя и адрес клиента не должны быть в каждом ряду таблицы фактов. Вместо этого в таблице фактов и таблице измерений может совместно использоваться идентификатор клиента. Запрос может объединить две таблицы, чтобы связать профиль и транзакции клиента.

- **Таблицы интеграции** служат для интеграции или промежуточного размещения данных. Можно создать таблицу интеграции в виде обычной таблицы, внешней таблицы или временной таблицы. Например, вы можете загружать данные в промежуточную таблицу, выполнять преобразования данных в режиме промежуточного размещения, а затем вставлять их в рабочую таблицу.

## <a name="schema-names"></a>имена схем;

Схемы являются хорошим способом сгруппировать объекты, которые используются аналогичным образом. Следующий код создает [схему, определяемую пользователем,](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) называемую wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Имена таблиц

Если вы переносите несколько баз данных из предварительного решения в пул S-L, наилучшей практикой является перенос всех таблиц факта, измерения и интеграции в одну схему пула S'L. Например, можно хранить все таблицы в хранилище данных [образеца WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) в одной схеме, называемой wwi.

Чтобы показать организацию таблиц в пуле S'L, можно использовать факты, тусклые и int в качестве префиксов к именам таблиц. В таблице ниже показаны некоторые из схем и названий таблиц для WideWorldImportersDW.  

| Таблица WideWorldImportersDW  | Тип таблицы | Бассейн СЗЛ |
|:-----|:-----|:------|:-----|
| Город | Измерение | wwi.DimCity |
| Порядок | Факты | wwi.FactOrder |

## <a name="table-persistence"></a>Сохраняемость таблицы

Таблицы хранят данные либо постоянно в Хранилище Azure, временно в Хранилище Azure, либо в хранилище данных, внешнем хранилище данных.

### <a name="regular-table"></a>Обычная таблица

Данные обычной таблицы хранятся в службе хранилища Azure как часть хранилища данных. Таблица и данные сохраняются независимо от того, является ли сеанс открытым.  Приведенный ниже пример создает обычную таблицу с двумя столбцов.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Временная таблица

Временная таблица существует, только пока отрыт сеанс. Можно использовать временную таблицу, чтобы другие пользователи не видели временных результатов. Использование временных таблиц также уменьшает необходимость очистки.  Временные таблицы используют локальное хранилище и, в пуле S'L, могут обеспечить более высокую производительность.  

По требованию S'L поддерживает временные таблицы. Но его использование ограничено, так как вы можете выбрать из временной таблицы, но не можете присоединиться к нему с файлами в хранилище.

Дополнительные сведения см. в статье [Временные таблицы в хранилище данных SQL](develop-tables-temporary.md).

### <a name="external-table"></a>Внешняя таблица

[Внешние таблицы](develop-tables-external-tables.md) указывают на данные, расположенные в blob Хранилища Azure или Хранилище озер данных Azure.

Импортные данные из внешних таблиц в пул СЗЛ с помощью заявления [CREATE TABLE AS SELECT.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Руководство по загрузке см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с помощью PolyBase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Для s'L по запросу можно использовать [CETAS](develop-tables-cetas.md) для сохранения результата запроса в внешней таблице в Azure Storage.

## <a name="data-types"></a>Типы данных

Пулс S'L поддерживает наиболее часто используемые типы данных. Список поддерживаемых типов данных представлен в [справочнике по типах данных в инструкции CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes). Для получения дополнительной информации [Data types](../sql/develop-tables-data-types.md)об использовании типов данных см.

## <a name="distributed-tables"></a>Распределенные таблицы

Фундаментальной особенностью пула S'L является способ его хранения и работы на столах между [дистрибутивами.](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)  Пул S'L поддерживает три метода распространения данных:

- Циклический перебор (по умолчанию)
- Хэш
- Реплицированный

### <a name="hash-distributed-tables"></a>Таблицы с хэш-распределением

Распределенная таблица хэша распределяет строки на основе значения в столбце распределения. Таблица распределения хэша предназначена для достижения высокой производительности запросов на больших таблицах. При выборе столбца распределения необходимо учитывать несколько факторов.

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Реплицированные таблицы

Копия реплицированной таблицы хранится на каждом вычислительном узле. Запросы быстро работают на реплицируемых таблицах, поскольку соединения на реплицированных таблицах не требуют движения данных. Репликация требует дополнительного хранения, хотя и не практично для больших таблиц.

Дополнительные сведения см. в статье [Руководство по проектированию для использования реплицированных таблиц в хранилище данных SQL Azure](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Таблицы с распределением методом циклического перебора

Строки таблицы с распределением методом циклического перебора распределяются равномерно по всем распределениям. Строки распределяются случайным образом. Загрузка данных в таблицу с распределением методом циклического перебора происходит быстро.  Но запросы могут потребовать большего количества передачи данных, чем другие методы распределения.

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Общие методы распределения для таблиц

Категория таблицы часто определяет оптимальный вариант для распределения таблицы.

| Категории таблицы | Рекомендуемый вариант распределения |
|:---------------|:--------------------|
| Факты           | Используйте хэш-распределение с кластеризованным индексом columnstore. Производительность улучшается, когда две хэш-таблицы объединены по одному столбцу распределения. |
| Измерение      | Используйте репликацию для небольших таблиц. Если таблицы слишком велики для хранения на каждом вычислительном узле, используйте хэш-распределение. |
| Промежуточная        | Используйте циклический перебор для промежуточных таблиц. Загрузка с помощью инструкции CTAS выполняется быстро. Как только данные поставляются в таблице, используйте INSERT... SELECT для перемещения данных в производственные таблицы. |

## <a name="partitions"></a>Секции

В пуле S'L разделительтаблица таблицы хранит и выполняет операции на строках таблицы в соответствии с диапазонами данных. Например, таблицу можно разделить по дню, месяцу или году. Вы можете улучшить производительность запросов путем исключения секций, что ограничивает проверку запросов к данным в секции.

Вы также можете управлять данными путем переключения разделов. Так как данные в пуле S'L уже распределены, слишком много разделов может замедлить производительность запроса. Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> При переключении раздела в непустые разделы, рассмотрите возможность использования TRUNCATE_TARGET опции в выписке [ALTER TABLE,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) если существующие данные должны быть усечены.

Приведенный ниже код переключает преобразованные ежедневные данные в раздел SalesFact и перезаписывает любые существующие данные.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

В S'L по требованию можно ограничить файлы/папки (разделы), которые будут прочитаны вашим запросом. Раздел по пути поддерживается с помощью функций filepath и fileinfo, описанных в [файлах хранения запросов.](develop-storage-files-overview.md) В следующем примере считывается папка с данными за 2017 год:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Индексы columnstore

По умолчанию пул s'L хранит таблицу в виде кластерного индекса столбцов. Такая форма хранения данных обеспечивает высокое сжатие данных и производительность запросов в больших таблицах.  Кластеризованный индекс columnstore обычно является лучшим выбором, но в некоторых случаях подходящей структурой хранения являются кластеризованный индекс или куча.  

> [!TIP]
> Таблица кучи может быть особенно полезна для загрузки переходных данных, таких как таблица, которая преобразуется в финальный стол.

Список функций индексов columnstore см. в статье [Новые возможности индексов columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Сведения о повышении производительности индекса columnstore см. в статье [Максимальное повышение качества группы строк для индекса columnstore](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Статистика


Оптимизатор запросов использует статистику уровня столбца при создании плана выполнения запроса. Для повышения производительности запроса важно иметь статистику по отдельным столбцам, особенно столбцов, используемым в соединениях запросов. Синапсе СЗЛ поддерживает автоматическое создание статистики. 

Статистическое обновление не происходит автоматически. Обновите статистику после добавления или изменения значительного числа строк. Например, обновить статистику после загрузки. Дополнительная информация содержится в статье [руководства по статистике.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Первичный ключ и уникальный ключ

PRIMARY KEY поддерживается только тогда, когда используются NONCLUSTERED и NOT ENFORCED.  Ограничение УНИКУЕ поддерживается только при использовании NOT ENFORCED.  Для получения дополнительной информации ознакомьтесь со статьей [«Таблица таблицы»](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) с системой «СЗЛ».

## <a name="commands-for-creating-tables"></a>Команды для создания таблиц

Вы можете создать пустую таблицу. Вы также можете создать и заполнить таблицу результатами инструкции Select. Ниже приведены команды T-SQL для создания таблицы.

| Инструкция Т-SQL | Описание |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Создает пустую таблицу, определив все столбцы и параметры таблицы. |
| [СОЗДАНИЕ ВНЕШНЕЙ ТАБЛИЦЫ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Создает внешнюю таблицу. Определение таблицы хранится в пуле S'L. Данные таблицы хранятся в хранилище Azure Blob или хранилище озер данных Azure. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Задает новую таблицу с результатами инструкции Select. Столбцы и типы данных таблицы основаны на результатах инструкции Select. Чтобы импортировать данные, эта инструкция может выбрать данные из внешней таблицы. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Создает новую внешнюю таблицу, экспортируя результаты инструкции Select во внешнее расположение.  Местоположение — это либо хранилище Azure Blob, либо хранилище озер данных Azure. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Согласование исходных данных с хранилищем данных

Таблицы хранилища данных заполняются путем загрузки данных из другого источника данных. Для успешной загрузки число и типы данных столбцов в исходных данных должны согласовываться с определением таблицы в хранилище данных.

> [!NOTE]
> Согласование данных может оказаться самой сложной частью разработки таблиц.

Если данные поступают из нескольких хранилики данных, можно портировать данные в хранилище данных и хранить их в таблице интеграции. После того, как данные находятся в таблице интеграции, можно использовать возможность пула S'L для реализации операций преобразования. После подготовки данных их можно вставить в рабочие таблицы.

## <a name="unsupported-table-features"></a>Неподдерживаемые функции таблиц

Пул s'L поддерживает многие, но не все функции таблицы, предлагаемые другими базами данных.  В следующем списке показаны некоторые функции таблицы, которые не поддерживаются в пуле S'L.

- Иностранный ключ, проверка [Таблица Ограничения](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Вычисляемые столбцы](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Индексированные представления](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Последовательности](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Разреженные столбцы](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Суррогатные ключи, реализуемые с [идентификацией](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Синонимы](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Триггеры](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Уникальные индексы](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Определяемые пользователем типы](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Запросы размера таблицы

Один простой способ определить пространство и строки, потребляемые таблицей в каждом из 60 дистрибутивов, заключается в использовании [dBCC PDW_SHOWSPACEUSED.](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Имейте в виду, что использование команд DBCC может быть весьма ограниченным.  Динамические административные представления (DMV) отображают больше сведений, чем команды DBCC. Начните с создания представления ниже.

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

Этот запрос возвращает количество строк и объем каждой таблицы.  Резюме пространства таблицы позволяет увидеть, какие таблицы являются самыми большими таблицами. Вы также увидите, являются ли они круговой, реплицируется или хэш-распределены.  Для таблиц с хэш-распределением запрос показывает столбец распределения.  

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

После создания таблицы для хранилища данных загрузите данные в таблицу.  Для руководства по [Loading data into SQL pool](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)загрузке см.
