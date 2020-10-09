---
title: Создание пакетов ELT вместо ETL
description: Реализация гибкой стратегии загрузки данных для пула Synapse SQL в Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1b73b82b4367d50cc5fbe9881a67e0afa041db86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201164"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Стратегии загрузки данных для пула Synapse SQL

Традиционные пулы SQL SMP используют процесс извлечения, преобразования и загрузки (ETL) для загрузки данных. Пул Synapse SQL в Azure Synapse Analytics — это архитектура массовой параллельной обработки (MPP), использующая преимущество масштабируемости и гибкости ресурсов вычислений и хранения.

Применение процесса извлечения, загрузки и преобразования (ELT) использует MPP и исключает ресурсы, необходимые для преобразования данных перед загрузкой.

Хотя пул SQL поддерживает множество методов загрузки, включая популярные варианты SQL Server, такие как [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), загрузка через внешние таблицы PolyBase и [инструкцию COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (предварительная версия) является самым быстрым и масштабируемым способом загрузки данных.

С помощью PolyBase и инструкции COPY можно обращаться к внешним хранимым данным в хранилище BLOB-объектов Azure или Azure Data Lake Storage, используя язык T-SQL. Для наибольшей гибкости при загрузке рекомендуем использовать инструкцию COPY.

> [!NOTE]  
> Сейчас инструкция COPY находится на этапе общедоступной предварительной версии. Для отзыва отправьте сообщение на этот адрес: sqldwcopypreview@service.microsoft.com.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Что такое ELT?

Извлечение, загрузка и преобразование (ELT) — это процесс, с помощью которого данные извлекаются из исходной системы, загружаются в пул SQL, а затем преобразовываются.

Основные шаги по реализации ELT:

1. Извлеките исходные данные в текстовые файлы.
2. Поместите данные в хранилище BLOB-объектов Azure или Azure Data Lake Store.
3. Подготовьте данные для загрузки.
4. Загрузите данные в промежуточные таблицы с помощью PolyBase или команды COPY.
5. Преобразуйте данные.
6. Вставьте данные в рабочие таблицы.

Руководство по загрузке см. в статье [Загрузка данных из хранилища BLOB-объектов Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Извлечение исходных данных в текстовые файлы

Получение данных за пределами исходной системы зависит от расположения хранилища. Целью является перемещение данных в Поддерживаемые текстовые или CSV-файлы.

### <a name="supported-file-formats"></a>Поддерживаемые типы файлов

С помощью PolyBase и инструкции COPY можно загружать данные из CSV-файлов или текстовых файлов с разделителями в кодировке UTF-8 и UTF-16. Кроме CSV-файлов или текстовых файлов с разделителями, данные также загружаются из форматов файлов Hadoop, таких как ORC и Parquet. PolyBase и инструкция COPY также могут загрузить данные из сжатых файлов Gzip и Snappy.

Не поддерживаются расширенная кодировка ASCII, форматы с фиксированной шириной и вложенные форматы, такие как WinZip или XML. Если вы выполняете экспорт из SQL Server, можно воспользоваться [программой командной строки bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для экспорта данных в текстовые файлы с разделителями.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Помещение данных в хранилище BLOB-объектов Azure или Azure Data Lake Storage

Чтобы поместить данные в службу хранилища Azure, переместите их в [хранилище BLOB-объектов Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) или [Azure Data Lake Store 2-го поколения](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). В любом расположении данные должны храниться в текстовых файлах. PolyBase и инструкция COPY могут загрузить их из любого расположения.

Ниже приведены средства и службы, которые можно использовать для перемещения данных в службу хранилища Azure.

- Служба [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) повышает пропускную способность сети, производительность, а также предсказуемое поведение. ExpressRoute — это служба, которая направляет данные с помощью выделенного частного подключения в Azure. Подключения ExpressRoute не направляют данные через общедоступный Интернет. Они отличаются повышенной надежностью, более высокой скоростью, меньшей задержкой и дополнительной безопасностью по сравнению с обычными подключениями через общедоступный Интернет.
- [Служебная программа AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) перемещает данные в службу хранилища Azure через общедоступный Интернет. Этот способ оптимален, если размер данных не превышает 10 ТБ. Для выполнения загрузок на регулярной основе с помощью AZCopy проверьте скорость сети, чтобы просмотреть, подходит ли она.
- [Фабрика данных Azure (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) включает шлюз, который можно установить на локальном сервере. Затем можно создать конвейер для перемещения данных из локального сервера в службу хранилища Azure. Сведения об использовании Фабрики данных с пулом SQL см. на странице [Загрузка данных для пула SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Подготовка данных для загрузки

Может потребоваться подготовка и очистка данных в учетной записи хранения перед их загрузкой. Подготовить данные можно, пока они хранятся в источнике, при экспорте данных в текстовые файлы или после того, как данные окажутся в службе хранилища Azure.  Лучше всего как можно раньше начать работу с данными.  

### <a name="define-the-tables"></a>Определение таблиц

При использовании инструкции COPY необходимо сначала определить таблицы, которые вы загружаете в пул SQL.

Если вы используете PolyBase, перед загрузкой данных необходимо определить внешние таблицы в пуле SQL. PolyBase использует внешние таблицы для определения данных и доступа к ним в службе хранилища Azure. Внешняя таблица аналогична представлению базы данных. Внешняя таблица содержит схему таблицы и указывает на данные, хранящиеся за пределами пула SQL.

Определение внешних таблиц включает указание источника данных, формата текстовых файлов и определений таблицы. Ниже приведены справочные статьи о синтаксисе T-SQL, которые вам понадобятся:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

При загрузке файлов Parquet используйте следующее сопоставление типов данных SQL:

|                         Тип Parquet                         |   Логический тип Parquet (заметка)   |  Тип данных SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY / BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      FLOAT       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       INT        |
|                            INT64                             |                                       |      BIGINT      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                STRING                 |     nvarchar     |
|                            BINARY                            |                 ENUM                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     Decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     Decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max)   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       INT        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       INT        |
|                            INT32                             |           INT(32, false)            |      BIGINT      |
|                            INT32                             |                 DATE                  |       Дата       |
|                            INT32                             |                DECIMAL                |     Decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64, true)            |      BIGINT      |
|                            INT64                             |           INT(64, false)            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     Decimal      |
|                            INT64                             |         TIME (MICROS / NANOS)         |       time       |
|                            INT64                             | TIMESTAMP (MILLIS / MICROS / NANOS) |    datetime2     |
| [Сложный тип](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 Список                  |   varchar(max)   |
| [Сложный тип](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |



Пример создания внешних объектов см. в разделе [Создание внешних таблиц](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Форматирование текстовых файлов

При использовании PolyBase в определенных внешних объектах необходимо выровнять строки текстовых файлов с внешней таблицей и определением формата файла. Данные в каждой строке текстового файла должны совпадать с определением таблицы.
Для форматирования текстовых файлов сделайте следующее:

- Если данные поступают из нереляционного источника, необходимо преобразовать их в строки и столбцы. Независимо от того, поступают ли данные из реляционного или нереляционного источника, их необходимо преобразовать для соответствия определениям столбцов таблицы, в которую вы планируете загрузить данные.
- Форматируйте данные в текстовом файле для соответствия определениям столбцов и типам данных в целевой таблице. Неполное соответствие между типами данных во внешних текстовых файлах и таблице пула SQL вызовет отклонение строк во время загрузки.
- Отделите поля в текстовом файле символом завершения.  Обязательно используйте уникальный символ или последовательность символов. Используйте указанный символ завершения для [создания формата внешнего файла](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Загрузка данных с помощью PolyBase или инструкции COPY

Этот метод рекомендуется для загрузки данных в промежуточную таблицу. Промежуточные таблицы позволяют обрабатывать ошибки без оказания влияния на рабочие таблицы. Промежуточная таблица также дает возможность использовать архитектуру параллельной обработки пула SQL для преобразований данных перед вставкой данных в рабочие таблицы.

### <a name="options-for-loading"></a>Параметры загрузки

Для загрузки данных можно использовать любой из следующих вариантов загрузки:

- [Инструкция Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) является рекомендуемой программой загрузки, так как она позволяет легко и гибко загружать данные. Инструкция имеет множество дополнительных возможностей загрузки, которые не предоставляет Polybase. 
- [Polybase с T-SQL](load-data-from-azure-blob-storage-using-polybase.md) требует определения внешних объектов данных.
- [PolyBase и инструкция COPY с Фабрикой данных Azure (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) представляют собой другое средство оркестрации.  Оно определяет конвейер и планирует расписания заданий.
- [Polybase с SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) хорошо работает, когда исходные данные находятся в SQL Server. Службы SSIS определяют сопоставления исходной и целевой таблиц, а также управляют загрузкой. При наличии пакетов служб SSIS можно изменить пакеты для работы с новым назначением хранилища данных.
- [PolyBase с Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) передает данные из таблицы в кадр данных Databricks и (или) записывает данные из кадра данных Databricks в таблицу с помощью PolyBase.

### <a name="other-loading-options"></a>Другие варианты загрузки

Помимо PolyBase и инструкции COPY можно использовать программу [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) или [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). bcp загружает данные напрямую в базу данных, минуя хранилище BLOB-объектов Azure, и предназначается только для небольших загрузок.

> [!NOTE]
> Производительность загрузки этих вариантов ниже, чем у PolyBase и инструкции COPY.

## <a name="5-transform-the-data"></a>5. Преобразование данных

Пока данные находятся в промежуточной таблице, выполните преобразования, необходимые для рабочей нагрузки. Затем переместите данные в рабочую таблицу.

## <a name="6-insert-the-data-into-production-tables"></a>6. Вставка данных в рабочие таблицы

С помощью инструкции INSERT INTO... SELECT данные перемещаются из промежуточной таблицы в постоянную.

При разработке процесса ETL попробуйте запустить его для небольшого тестового примера. Попробуйте извлечь 1000 строк из таблицы в файл, переместить его в Azure, а затем загрузить в промежуточную таблицу.

## <a name="partner-loading-solutions"></a>Партнерские решения для загрузки

Многие из наших партнеров предлагают решения для загрузки. Дополнительные сведения см. в статье [Партнеры по бизнес-аналитике хранилища данных SQL](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по загрузке см. [здесь](guidance-for-loading-data.md).
