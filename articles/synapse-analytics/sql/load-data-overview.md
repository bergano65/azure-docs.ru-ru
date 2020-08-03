---
title: Проектирование стратегии загрузки данных Polybase для пула SQL
description: Вместо ETL Создайте процесс извлечения, загрузки и преобразования (ELT) для загрузки данных или пула SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 31e1eb952bb37f5864e296811ba6e61bb0e58320
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87490291"
---
# <a name="design-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Проектирование стратегии загрузки данных Polybase для пула SQL Azure синапсе

Традиционные хранилища данных SMP используют процесс извлечения, преобразования и загрузки (ETL) для загрузки данных. Пул SQL Azure — это архитектура с массовой параллельной обработкой (MPP), которая использует преимущества масштабируемости и гибкости ресурсов вычислений и хранилища. С помощью процесса извлечения, загрузки и преобразования (ELT) можно воспользоваться преимуществами MPP и устранить ресурсы, необходимые для преобразования данных перед их загрузкой.

Хотя пул SQL поддерживает множество методов загрузки, включая параметры, отличные от polybase, такие как BCP и SQL BulkCopy API, самый быстрый и наиболее масштабируемый способ загрузки даты — с помощью Polybase.  PolyBase — это технология, которая обращается к внешним данным, хранящимся в хранилище BLOB-объектов Azure или Azure Data Lake Storage, с помощью языка T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Извлечение, Загрузка и преобразование (ELT)

Извлечение, Загрузка и преобразование (ELT) — это процесс, с помощью которого данные извлекаются из исходной системы, загружаются в хранилище данных, а затем преобразуются.

Ниже приведены основные шаги для реализации ELT в пуле SQL для Polybase.

1. Извлеките исходные данные в текстовые файлы.
2. Поместите данные в хранилище BLOB-объектов Azure или Azure Data Lake Store.
3. Подготовьте данные для загрузки.
4. Загрузка данных в промежуточные таблицы пула SQL с помощью Polybase.
5. Преобразуйте данные.
6. Вставьте данные в рабочие таблицы.

Инструкции по загрузке см. в руководстве [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с помощью PolyBase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Дополнительные сведения см. в записи блога о [стратегиях и шаблонах загрузки в хранилище данных SQL Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Извлечение исходных данных в текстовые файлы

Получение данных за пределами исходной системы зависит от расположения хранилища.  Основной целью является перемещение данных в текстовые файлы с разделителями, поддерживаемыми PolyBase.

### <a name="polybase-external-file-formats"></a>Форматы внешних файлов PolyBase

PolyBase загружает данные из текстовых файлов с разделителями в кодировке UTF-8 и UTF-16. Кроме текстовых файлов с разделителями данные также загружаются из форматов файлов Hadoop: RC, ORC и PARQUET. PolyBase также может загрузить данные из сжатых файлов Gzip и Snappy. PolyBase в настоящее время не поддерживает расширенную кодировку ASCII, форматы с фиксированной шириной и вложенные форматы, такие как WinZip, JSON и XML.

Если вы выполняете экспорт из SQL Server, можно воспользоваться [программой командной строки bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для экспорта данных в текстовые файлы с разделителями. Сопоставление типов данных Parquet и SQL DW выглядит следующим образом:

| **Тип данных Parquet** |                      **Тип данных SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          INT          |                             INT                              |
|        BIGINT         |                            BIGINT                            |
|        boolean        |                             bit                              |
|        double         |                            FLOAT                             |
|         FLOAT         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       TIMESTAMP       |                             Дата                             |
|       TIMESTAMP       |                        smalldatetime                         |
|       TIMESTAMP       |                          datetime2                           |
|       TIMESTAMP       |                           DATETIME                           |
|       TIMESTAMP       |                             time                             |
|       Дата            |                             Дата                             |
|        Decimal        |                            Decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Помещение данных в хранилище BLOB-объектов Azure или Azure Data Lake Storage

Чтобы поместить данные в службу хранилища Azure, их можно переместить в [хранилище BLOB-объектов Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) или [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). В любом расположении данные должны храниться в текстовых файлах. PolyBase может загрузить их из любого расположения.

Ниже приведены средства и службы, которые можно использовать для перемещения данных в службу хранилища Azure.

- Служба [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) повышает пропускную способность сети, производительность, а также предсказуемое поведение. ExpressRoute — это служба, которая направляет данные с помощью выделенного частного подключения в Azure. Подключения ExpressRoute не направляют данные через общедоступный Интернет. Они отличаются повышенной надежностью, более высокой скоростью, меньшей задержкой и дополнительной безопасностью по сравнению с обычными подключениями через общедоступный Интернет.
- [Служебная программа AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) перемещает данные в службу хранилища Azure через общедоступный Интернет. Этот способ оптимален, если размер данных не превышает 10 ТБ. Для выполнения загрузок на регулярной основе с помощью AZCopy проверьте скорость сети, чтобы просмотреть, подходит ли она.
- [Фабрика данных Azure (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) включает шлюз, который можно установить на локальном сервере. Затем можно создать конвейер для перемещения данных из локального сервера в службу хранилища Azure. Сведения об использовании фабрики данных с пулом SQL см. в статье [Загрузка данных в пул SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Подготовка данных для загрузки

Возможно, потребуется подготовить и очистить данные в учетной записи хранения перед их загрузкой в пул SQL. Подготовить данные можно, пока они хранятся в источнике, при экспорте данных в текстовые файлы или после того, как данные окажутся в службе хранилища Azure.  Лучше всего как можно раньше начать работу с данными.  

### <a name="define-external-tables"></a>Определение внешних таблиц

Перед загрузкой данных необходимо определить внешние таблицы в хранилище данных. PolyBase использует внешние таблицы для определения данных и доступа к ним в службе хранилища Azure. Внешняя таблица аналогична представлению базы данных. Внешняя таблица содержит схему таблицы и указывает на данные, хранящиеся за пределами хранилища данных.

Определение внешних таблиц включает указание источника данных, формата текстовых файлов и определений таблицы. Ниже приведены подразделы синтаксиса T-SQL, которые вам понадобятся:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="format-text-files"></a>Форматирование текстовых файлов

После определения внешних объектов необходимо выровнять строки текстовых файлов с внешней таблицей и определением формата файла. Данные в каждой строке текстового файла должны совпадать с определением таблицы.
Для форматирования текстовых файлов сделайте следующее:

- Если данные поступают из нереляционного источника, необходимо преобразовать их в строки и столбцы. Независимо от того, поступают ли данные из реляционного или нереляционного источника, их необходимо преобразовать для соответствия определениям столбцов таблицы, в которую вы планируете загрузить данные.
- Форматирование данных в текстовом файле для согласования с столбцами и типами данных в целевой таблице пула SQL. Неполное соответствие между типами данных во внешних текстовых файлах и таблице хранилища данных вызовет отклонение строк во время загрузки.
- Отделите поля в текстовом файле символом завершения.  Обязательно используйте уникальный символ или последовательность символов. Используйте указанный символ завершения для [создания формата внешнего файла](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Загрузка данных в промежуточные таблицы пула SQL с помощью Polybase

Этот метод рекомендуется для загрузки данных в промежуточную таблицу. Промежуточные таблицы позволяют обрабатывать ошибки без оказания влияния на рабочие таблицы. Промежуточная таблица также дает возможность использовать MPP-пул SQL для преобразований данных перед вставкой данных в рабочие таблицы.

### <a name="options-for-loading-with-polybase"></a>Варианты загрузки данных с помощью PolyBase

Чтобы загрузить данные с помощью PolyBase, можно использовать любые из приведенных ниже вариантов загрузки.

- [PolyBase с использованием T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) хорошо работает, когда данные хранятся в хранилище BLOB-объектов Azure или Azure Data Lake Store. Этот вариант предоставляет наибольший контроль над процессом загрузки, но также требует определения объектов внешних данных. Другие методы определяют эти объекты в фоновом режиме, когда вы сопоставляете исходные таблицы с целевыми.  Для оркестрации загрузок T-SQL можно использовать фабрику данных Azure, службы SSIS или функции Azure.
- [Polybase с SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) хорошо работает, когда исходные данные находятся в SQL Server. Службы SSIS определяют сопоставления исходной и целевой таблиц, а также управляют загрузкой. При наличии пакетов служб SSIS можно изменить пакеты для работы с новым назначением хранилища данных.
- [PolyBase с фабрикой данных Azure (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) представляет собой другое средство оркестрации.  Оно определяет конвейер и планирует расписания заданий.
- [Polybase с Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) передает данные из таблицы хранилища данных SQL в таблицу данных и (или) записывает данные из таблицы DataTables из модуля данные в таблице хранилища SQL Server с помощью polybase.

### <a name="non-polybase-loading-options"></a>Варианты загрузки, отличные от PolyBase

Если данные несовместимы с PolyBase, можно использовать программу [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) или [API-интерфейс SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Программа bcp загружается непосредственно в пул SQL без помощи хранилища BLOB-объектов Azure и предназначена только для небольших нагрузок. Обратите внимание, что производительность загрузки этих вариантов значительно ниже, чем у PolyBase.

## <a name="5-transform-the-data"></a>5. Преобразование данных

Пока данные находятся в промежуточной таблице, выполните преобразования, необходимые для рабочей нагрузки. Затем переместите данные в рабочую таблицу.

## <a name="6-insert-the-data-into-production-tables"></a>6. Вставка данных в рабочие таблицы

С помощью инструкции INSERT INTO... SELECT данные перемещаются из промежуточной таблицы в постоянную.

При разработке процесса ETL попробуйте запустить его для небольшого тестового примера. Попробуйте извлечь 1000 строк из таблицы в файл, переместить его в Azure, а затем загрузить в промежуточную таблицу.

## <a name="partner-loading-solutions"></a>Партнерские решения для загрузки

Многие из наших партнеров предлагают решения для загрузки. Дополнительные сведения см. в статье [Партнеры по бизнес-аналитике хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по загрузке см. [здесь](data-loading-best-practices.md).
