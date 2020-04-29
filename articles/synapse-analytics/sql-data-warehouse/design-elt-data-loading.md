---
title: В отличие от ETL, проектный ELT
description: Реализуйте гибкие стратегии загрузки данных для пула SQL синапсе в Azure синапсе Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e99fd898956e11a4827d023691111a47e5a790c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744957"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Стратегии загрузки данных для пула SQL синапсе

Традиционные пулы SQL для SMP используют процесс извлечения, преобразования и загрузки (ETL) для загрузки данных. Синапсе пул SQL в Azure синапсе Analytics имеет архитектуру с массовой параллельной обработкой (MPP), которая использует преимущества масштабируемости и гибкости ресурсов вычислений и хранилища.

Используя процесс извлечения, загрузки и преобразования (ELT), вы используете MPP и устраняете ресурсы, необходимые для преобразования данных до загрузки.

Хотя пул SQL поддерживает множество методов загрузки, включая популярные параметры SQL Server, такие как [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), самый быстрый и наиболее масштабируемый способ загрузки данных — внешние таблицы polybase и [инструкция Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Предварительная версия).

С помощью Polybase и инструкции COPY можно получить доступ к внешним данным, хранящимся в хранилище BLOB-объектов Azure, или Azure Data Lake Store с помощью языка T-SQL. Для наибольшей гибкости при загрузке рекомендуется использовать инструкцию COPY.

> [!NOTE]  
> Сейчас инструкция COPY находится на этапе общедоступной предварительной версии. Чтобы оставить отзыв, отправьте сообщение электронной почты по следующему списку рассылки sqldwcopypreview@service.microsoft.com:.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Что такое ELT?

Извлечение, Загрузка и преобразование (ELT) — это процесс, с помощью которого данные извлекаются из исходной системы, загружаются в пул SQL, а затем преобразуются.

Ниже приведены основные шаги для реализации ELT.

1. Извлеките исходные данные в текстовые файлы.
2. Поместите данные в хранилище BLOB-объектов Azure или Azure Data Lake Store.
3. Подготовьте данные для загрузки.
4. Загрузите данные в промежуточные таблицы с помощью Polybase или команды COPY.
5. Преобразуйте данные.
6. Вставьте данные в рабочие таблицы.

Руководство по загрузке в Polybase см. [в статье Загрузка данных из хранилища BLOB-объектов Azure с помощью polybase](load-data-from-azure-blob-storage-using-polybase.md).

Дополнительные сведения см. в записи блога о [стратегиях и шаблонах загрузки в хранилище данных SQL Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Извлечение исходных данных в текстовые файлы

Получение данных за пределами исходной системы зависит от расположения хранилища.  Целью является перемещение данных в Polybase и копирование поддерживаемых текстовых или CSV-файлов с разделителями.

### <a name="polybase-and-copy-external-file-formats"></a>Polybase и копирование форматов внешних файлов

С помощью Polybase и инструкции COPY можно загружать данные из текстовых или CSV-файлов с разделителями в кодировке UTF-8 и кодировке UTF – 16. Помимо текстовых или CSV-файлов, он загружается из форматов файлов Hadoop, таких как ORC и Parquet. Polybase и инструкция COPY также могут загружать данные из файлов gzip и привязывают сжатые файлы.

Расширенные символы ASCII, формат с фиксированной шириной и вложенные форматы, такие как WinZip или XML, не поддерживаются. При экспорте из SQL Server можно использовать [программу командной строки bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для экспорта данных в текстовые файлы с разделителями.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Наземный данные в хранилище BLOB-объектов Azure или Azure Data Lake Store

Чтобы разместить данные в службе хранилища Azure, можно переместить их в [хранилище BLOB-объектов Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) или [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). В любом расположении данные должны храниться в текстовых файлах. Polybase и инструкция COPY могут загружаться из любого расположения.

Ниже приведены средства и службы, которые можно использовать для перемещения данных в службу хранилища Azure.

- Служба [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) повышает пропускную способность сети, производительность, а также предсказуемое поведение. ExpressRoute — это служба, которая направляет данные с помощью выделенного частного подключения в Azure. Подключения ExpressRoute не направляют данные через общедоступный Интернет. Они отличаются повышенной надежностью, более высокой скоростью, меньшей задержкой и дополнительной безопасностью по сравнению с обычными подключениями через общедоступный Интернет.
- [Служебная программа AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) перемещает данные в службу хранилища Azure через общедоступный Интернет. Этот способ оптимален, если размер данных не превышает 10 ТБ. Для выполнения загрузок на регулярной основе с помощью AZCopy проверьте скорость сети, чтобы просмотреть, подходит ли она.
- [Фабрика данных Azure (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) включает шлюз, который можно установить на локальном сервере. Затем можно создать конвейер для перемещения данных из локального сервера в службу хранилища Azure. Сведения об использовании фабрики данных с SQL Analytics см. в статье [Загрузка данных для SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Подготовка данных к загрузке

Перед загрузкой может потребоваться подготовить и очистить данные в учетной записи хранения. Подготовить данные можно, пока они хранятся в источнике, при экспорте данных в текстовые файлы или после того, как данные окажутся в службе хранилища Azure.  Лучше всего как можно раньше начать работу с данными.  

### <a name="define-external-tables"></a>Определение внешних таблиц

Если вы используете Polybase, перед загрузкой необходимо определить внешние таблицы в пуле SQL. В инструкции COPY внешние таблицы не требуются. PolyBase использует внешние таблицы для определения данных и доступа к ним в службе хранилища Azure.

Внешняя таблица аналогична представлению базы данных. Внешняя таблица содержит схему таблицы и указывает на данные, хранящиеся за пределами пула SQL.

Определение внешних таблиц включает указание источника данных, формата текстовых файлов и определений таблицы. Ниже приведены справочные статьи по синтаксису T-SQL.

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

При загрузке Parquet сопоставление типов данных SQL:

| **Тип данных Parquet** | **Тип данных SQL** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          INT          |        INT        |
|        BIGINT         |      BIGINT       |
|        Логическое        |        bit        |
|        double         |       FLOAT       |
|         FLOAT         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        строка         |       nchar       |
|        строка         |     nvarchar      |
|        строка         |       char        |
|        строка         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       TIMESTAMP       |       Дата        |
|       TIMESTAMP       |   smalldatetime   |
|       TIMESTAMP       |     datetime2     |
|       TIMESTAMP       |     DATETIME      |
|       TIMESTAMP       |       time        |
|         Дата          |       Дата        |
|        Decimal        |      Decimal      |

Пример создания внешних объектов см. в разделе [Создание внешних таблиц для примеров данных](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) в руководстве по загрузке.

### <a name="format-text-files"></a>Форматирование текстовых файлов

При использовании Polybase внешние объекты должны выстроить строки текстовых файлов с определением внешней таблицы и формата файла. Данные в каждой строке текстового файла должны совпадать с определением таблицы.
Для форматирования текстовых файлов сделайте следующее:

- Если данные поступают из нереляционного источника, необходимо преобразовать их в строки и столбцы. Независимо от того, поступают ли данные из реляционного или нереляционного источника, их необходимо преобразовать для соответствия определениям столбцов таблицы, в которую вы планируете загрузить данные.
- Форматирование данных в текстовом файле для согласования с столбцами и типами данных в целевой таблице. Неправильное выравнивание типов данных во внешних текстовых файлах и в таблице пула SQL приводит к тому, что строки будут отклонены во время загрузки.
- Отделите поля в текстовом файле символом завершения.  Не забудьте использовать символ или последовательность символов, которые не найдены в исходных данных. Используйте указанный символ завершения для [создания формата внешнего файла](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Загрузка данных с помощью Polybase или инструкции COPY

Этот метод рекомендуется для загрузки данных в промежуточную таблицу. Промежуточные таблицы позволяют обрабатывать ошибки без оказания влияния на рабочие таблицы. Промежуточная таблица также дает возможность использовать MPP-пул SQL для преобразований данных перед вставкой данных в рабочие таблицы.

Таблица должна быть предварительно создана при загрузке в промежуточную таблицу с КОПИРОВАНИЕм.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Параметры загрузки с помощью инструкций Polybase и COPY

Чтобы загрузить данные с помощью PolyBase, можно использовать любые из приведенных ниже вариантов загрузки.

- [PolyBase с использованием T-SQL](load-data-from-azure-blob-storage-using-polybase.md) хорошо работает, когда данные хранятся в хранилище BLOB-объектов Azure или Azure Data Lake Store. Этот вариант предоставляет наибольший контроль над процессом загрузки, но также требует определения объектов внешних данных. Другие методы определяют эти объекты в фоновом режиме, когда вы сопоставляете исходные таблицы с целевыми.  Для оркестрации загрузок T-SQL можно использовать фабрику данных Azure, службы SSIS или функции Azure.
- [PolyBase со службами SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) является оптимальным выбором, когда исходные данные хранятся в SQL Server — локально или в облаке. Службы SSIS определяют сопоставления исходной и целевой таблиц, а также управляют загрузкой. При наличии пакетов служб SSIS можно изменить пакеты для работы с новым назначением хранилища данных.
- [Polybase и инструкция Copy с фабрикой данных Azure (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) — еще одно средство оркестрации.  Оно определяет конвейер и планирует расписания заданий.
- [Polybase с Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) передает данные из таблицы в стек данных и (или) записывает данные из стека данных DataTables в таблицу с помощью polybase.

### <a name="other-loading-options"></a>Другие параметры загрузки

Помимо Polybase и инструкции COPY можно использовать [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) или [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Программа bcp загружается непосредственно в базу данных без помощи хранилища BLOB-объектов Azure и предназначена только для небольших нагрузок.

> [!NOTE]
> Производительность загрузки этих параметров ниже, чем у Polybase и инструкции COPY.

## <a name="5-transform-the-data"></a>5. Преобразование данных

Пока данные находятся в промежуточной таблице, выполните преобразования, необходимые для рабочей нагрузки. Затем переместите данные в рабочую таблицу.

## <a name="6-insert-the-data-into-production-tables"></a>6. Вставка данных в рабочие таблицы

Вставка в... Инструкция SELECT перемещает данные из промежуточной таблицы в постоянную таблицу.

При разработке процесса ETL попробуйте запустить его для небольшого тестового примера. Попробуйте извлечь 1000 строк из таблицы в файл, переместить его в Azure, а затем загрузить в промежуточную таблицу.

## <a name="partner-loading-solutions"></a>Партнерские решения для загрузки

Многие из наших партнеров предлагают решения для загрузки. Дополнительные сведения см. в статье [Партнеры по бизнес-аналитике хранилища данных SQL](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Дальнейшие шаги

Инструкции по загрузке см. [здесь](guidance-for-loading-data.md).
