---
title: Рекомендации по использованию пула Synapse SQL в Azure Synapse Analytics (ранее — хранилище данных SQL)
description: Рекомендации и лучшие методики разработки решений для пула SQL в Azure Synapse Analytics (ранее — хранилище данных SQL).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6978855e9b32a3842e76d02ef543d86cf0673019
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206654"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Рекомендации по использованию пула Synapse SQL в Azure Synapse Analytics (ранее — хранилище данных SQL)

В этой статье содержится ряд рекомендаций, которые помогут достичь оптимальной производительности развертывания [пула SQL](sql-data-warehouse-overview-what-is.md).  Здесь представлены основные рекомендации и важные аспекты, которые следует учитывать.  

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения о сокращении затрат при помощи приостановки и масштабирования см. в статье об [управлении вычислительными ресурсами](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Обеспечение статистики

Для пула SQL можно настроить автоматическое обнаружение и создание статистики по столбцам.  Планы запросов, созданные с помощью оптимизатора, зависят от доступной статистики.  

Рекомендуется включить AUTO_CREATE_STATISTICS для баз данных и обновлять статистику ежедневно или после каждой загрузки, чтобы гарантировать актуальность статистики по столбцам, используемых в запросах.

Если полное обновление статистики занимает слишком много времени, можно выбирать отдельные столбцы для более частого обновления. Например, можно ежедневно обновлять столбцы дат, в которые добавляются новые значения.

> [!TIP]
> Статистику рекомендуется обновлять в столбцах, которые входят в объединения, а также предложения WHERE и GROUP BY.

Дополнительные сведения см. в статьях [Управление статистикой таблиц](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов

Пул SQL имеет несколько динамических административных представлений, которые можно использовать для отслеживания выполнения запросов.  В статье о мониторинге рабочих нагрузок с помощью динамических административных представлений описаны пошаговые инструкции по отслеживанию выполнения запроса.  

Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL.

Дополнительные сведения см. в статьях [Мониторинг рабочей нагрузки пула Azure Synapse Analytics SQL с помощью динамических административных представлений](sql-data-warehouse-manage-monitor.md), [Использование меток для инструментирования запросов в хранилище данных SQL](sql-data-warehouse-develop-label.md), [Предложение OPTION (Transact-SQL)](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ys.dm_pdw_dms_workers (Transact-SQL), DBCC](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [PDW_SHOWEXECUTIONPLAN (Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [sys.dm_pdw_waits (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Настройка производительности запросов с помощью новых усовершенствований продукта

- [Performance tuning with materialized views](performance-tuning-materialized-views.md) (Настройка производительности с помощью материализованных представлений)
- [Performance tuning with result set caching](performance-tuning-ordered-cci.md) (Настройка производительности с помощью упорядоченного кластеризованного индекса columnstore)
- [Performance tuning with result set caching](performance-tuning-result-set-caching.md) (Настройка производительности путем кэширования результирующего набора)

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы

Однократную загрузку в небольшую таблицу или даже периодическую перезагрузку результатов поиска рекомендуется выполнять, используя такой синтаксис инструкции INSERT: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  

Однако для загрузки тысяч или миллионов строк на протяжении дня одноэлементных инструкций INSERT может быть недостаточно.  Вместо них можно создать процессы, которые будут записывать инструкции INSERT в файл и периодически загружать его.

Дополнительные сведения см. в статье [Инструкция INSERT (Transact-SQL)](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

Пул SQL поддерживает загрузку и экспорт данных при помощи нескольких инструментов, включая Фабрику данных Azure, PolyBase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  Однако для загрузки или экспорта данных большого объема, что требует высокой производительности, лучше всего использовать PolyBase.  

PolyBase использует архитектуру вычислений с массовым параллелизмом (MPP), поэтому загрузка и экспорт данных с помощью этого средства выполняется быстрее, чем с помощью любого другого.  Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO.  

> [!TIP]
> Команда CTAS позволяет минимизировать ведение журнала транзакций и ускорить загрузку данных.

Фабрика данных Azure также поддерживает нагрузки PolyBase и способна достичь производительности, сопоставимой с CTAS.  PolyBase поддерживает различные форматы файлов, включая формат GZIP.  
  
> [!NOTE]
> Чтобы максимально повысить пропускную способность при использовании текстовых файлов в формате GZIP, разбейте файлы на 60 или больше частей. Это позволит достичь максимальной степени параллелизма для загрузки.  Кроме того, для повышения общей пропускной способности можно загружать данные одновременно.

Дополнительные сведения см. в статьях [Загрузка данных в хранилище данных Azure SQL](design-elt-data-loading.md), [Руководство по использованию PolyBase в хранилище данных SQL](guidance-for-loading-data.md), [Стратегии и шаблоны загрузки данных в пул SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../), [Загрузка данных в хранилище данных SQL с помощью фабрики данных]( ../../data-factory/load-azure-sql-data-warehouse.md), [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure](../../data-factory/transform-data-using-machine-learning.md), [Создание формата внешнего файла (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [Функция Create Table As Select (CTAS) в хранилище данных SQL](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним

Технология Polybase (т. н. внешние таблицы), возможно, является самым быстрым способом загрузки данных, но она не оптимальна с точки зрения запросов. В настоящее время таблицы Polybase поддерживают только файлы больших двоичных объектов Azure и хранилище Azure Data Lake. Эти файлы не обслуживаются какими-либо вычислительными ресурсами.  

Поэтому пул SQL не может разгрузить эту операцию, и ему требуется прочитать файл целиком, загрузив его для этого в базу данных tempdb.  Таким образом, если будет отправлено несколько запросов к этим данным, то лучше один раз загрузить их в локальную таблицу и предоставить ее для выполнения запросов.

Ознакомьтесь также с [руководством по использованию PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц

По умолчанию таблицы распределяются по методу циклического перебора.  Это позволяет упростить процесс создания таблиц, так как пользователям не нужно принимать решение о типе распределения.  Таблицы с распределением по методу циклического перебора могут вполне годиться для некоторых рабочих нагрузок, но в большинстве случаев намного эффективнее использовать столбец распределения.  

Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  

Например, во время выполнения запроса на объединение таблицы Orders, распределенной по идентификатору order_id, с таблицей Transactions, распределенной по тому же идентификатору, этот запрос превращается в запрос к серверу, что исключает выполнение операций перемещения данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.  

> [!TIP]
> При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  

Ниже приведены ссылки на дополнительные сведения о том, как с помощью столбца распределения повысить производительность и как определить распределенную таблицу в предложение WITH инструкции CREATE TABLE.

Дополнительные сведения см. в статьях [Общие сведения о таблицах в хранилище данных SQL](sql-data-warehouse-tables-overview.md), [Распределение таблиц в хранилище данных SQL](sql-data-warehouse-tables-distribute.md), [Создание таблицы (хранилище данных Azure SQL)](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [Создание TABLE AS SELECT (хранилище данных Azure SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [CREATE TABLE AS SELECT (хранилище данных SQL Azure)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования

Хотя секционирование данных — это эффективный способ управления данными с помощью переключения секций или оптимизации сканирования путем исключения секций, наличие большого количества секций может замедлить выполнение запросов.  Стратегия разделения данных на большое количество секций, которая хорошо работает в SQL Server, не очень подходит для пула SQL.  

Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк.  Помните, что в секциях пула SQL данные секционируются на 60 баз данных, поэтому при создании таблицы со 100 секциями фактически будет создана таблица с 6000 секций.  

Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  В хранилище данных SQL можно использовать меньшее количество секций, чем в SQL Server.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

Дополнительные сведения см. в статье [Секционирование таблиц](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций

Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  

Например, если на выполнение инструкции INSERT требуется 1 час, по возможности разделите ее на 4 части, каждая из которых будет выполняться 15 минут.  К пустым таблицам можно применять специальные операции, которые сопровождаются записью в журнал минимальных сведений (такие как CTAS, TRUNCATE, DROP TABLE или INSERT), чтобы снизить риск отката.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо инструкции DELETE для удаления всех строк в таблице со значением order_date в октябре 2001 г., можно секционировать данные помесячно и переключить ненужную секцию на данные из пустой секции другой таблицы (см. примеры использования инструкции [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)).  

Для несекционированной таблицы можно применить вместо DELETE инструкцию CTAS, которая запишет данные в таблицу.  Выполнение CTAS займет столько же времени, и эта инструкция намного безопаснее, так как она выполняет мало записей в журнал о транзакциях и при необходимости ее можно быстро отменить.

Дополнительные сведения см. в статьях [Сведения о транзакциях](sql-data-warehouse-develop-transactions.md), [Оптимизация транзакций](sql-data-warehouse-develop-best-practices-transactions.md), [Секционирование таблиц](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [Функция Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Уменьшение размера результатов запроса

Этот шаг помогает избежать проблем на стороне клиента, вызванных большими результатами запросов.  Запрос можно изменить, чтобы уменьшить число возвращаемых строк. Некоторые инструменты создания запросов позволяют добавлять в каждый запрос синтаксис "первые N".  Можно также использовать инструкцию CETAS, чтобы записать результат запроса во временную таблицу, а затем использовать экспорт PolyBase для обработки на нижнем уровне.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца

При определении DDL рекомендуется использовать поддерживаемый тип данных с наименьшим размером. Это позволит повысить производительность запросов.  Такой подход особенно важен для столбцов CHAR и VARCHAR.  

Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

Дополнительные сведения см. в статьях [Разработка таблиц в пуле Synapse SQL](sql-data-warehouse-tables-overview.md), [Типы данных таблиц в пуле Synapse SQL](sql-data-warehouse-tables-data-types.md) и [CREATE TABLE (хранилище данных SQL Azure)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных

Если вам необходимо временно разместить данные, использование таблицы без кластеризованных индексов может существенно ускорить процесс.  Если вы загружаете данные только для промежуточного хранения перед их последующими преобразованиями, загрузка таблицы в таблицу кучи будет происходить значительно быстрее по сравнению с загрузкой данных в кластеризованную таблицу columnstore.  

Кроме того, загрузка данных во временную таблицу выполняется гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с # и доступны только сеансам, в которых они были созданы, поэтому их применимость ограничена.

Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Дополнительные сведения см. в статьях [Временные таблицы в пуле в SQL](sql-data-warehouse-tables-temporary.md), [CREATE TABLE (хранилище данных SQL Azure)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и [CREATE TABLE AS SELECT (хранилище данных SQL Azure)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore

Использование кластеризованных индексов columnstore — это один из наиболее эффективных способов хранения данных в пуле SQL.  По умолчанию в пуле SQL используются таблицы с кластеризованными индексами columnstore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  

Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  Качество сегмента можно изменить по числу строк в сжатой группе строк.  Дополнительные сведения об определении и улучшении качества сегмента для таблиц с кластеризованными индексами columnstore см. в разделе [Причины низкого качества индекса columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) статьи [Индексирование таблиц в пуле Synapse SQL](sql-data-warehouse-tables-index.md).  

Так как высококачественные сегменты columnstore важны, рекомендуется использовать для загрузки данных идентификаторы пользователей, которым назначен класс ресурсов среднего или большого размера. Использование [единиц использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Обычно таблицы columnstore не передают данные в сжатый сегмент columnstore, пока среднее количество строк в таблицах не превысит миллион, а каждая таблица пула SQL не будет секционирована на 60 таблиц. Поэтому нецелесообразно применять к таблицам columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Для таблицы с количеством строк менее 60 миллионов бессмысленно использовать индекс Columnstore, хотя он и не повлияет на производительность.  

Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица состоит из 100 секций, то для полезного применения столбцов columnstore она должна содержать не менее 6 миллиардов строк (60 распределений *100 секций* 1 миллион строк).  

Если таблица не содержит такого количества строк, рекомендуется уменьшить количество секций или использовать таблицу без кластеризованных индексов.  Чтобы получить более высокую производительность, возможно, вместо кластеризованной таблицы стоит использовать таблицу без кластеризованных индексов, содержащую вторичные индексы.

> [!TIP]
> Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Ознакомьтесь также со статьями [Индексирование таблиц в хранилище данных SQL](sql-data-warehouse-tables-index.md), [Руководство по индексам columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и разделом с описанием [повторной сборки индексов columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов

Чтобы выделить память для выполнения запросов, пул SQL использует группы ресурсов.  По умолчанию для всех пользователей настроен класс ресурсов небольшого размера, который выделяет по 100 МБ памяти для каждого распределения.  В хранилище данных SQL содержится 60 распределений, на каждое из которых выделяется как минимум 100 МБ памяти. Поэтому в целом для системы выделяется 6000 МБ памяти (около 6 ГБ).  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  А для некоторых других запросов, таких как запросы на обычное сканирование, разница будет незаметна.  Но использование более высокого класса снижает параллелизм, и это стоит учесть перед тем, как переводить всех пользователей на более высокий класс ресурсов.

См. также [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Использование класса ресурсов небольшого размера для увеличения параллелизма

Если во время выполнения пользовательских запросов возникают длительные задержки, возможно, выбраны классы ресурсов слишком большого размера или используется слишком иного слотов параллельности, что приводит к помещению других запросов в очередь.  Чтобы посмотреть очередь выполнения пользовательских запросов, выполните команду `SELECT * FROM sys.dm_pdw_waits`, которая позволяет просмотреть возвращенные строки.

См. сведения в статьях [Управление рабочими нагрузками с использованием классов ресурсов в Azure Synapse Analytics](resource-classes-for-workload-management.md) и [sys.dm_pdw_waits (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Другие ресурсы

Дополнительные сведения о распространенных проблемах и решениях см. в статье [Устранение неполадок хранилища данных SQL Azure](sql-data-warehouse-troubleshoot.md).

Если вы не нашли нужных сведений в этой статье, попробуйте использовать поиск по документации в левой части страницы, чтобы найти все документы, связанные с Azure Synapse.  На [странице вопросов и ответов Майкрософт для Azure Synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) можно задавать вопросы другим пользователям и группе разработчиков Azure Synapse. Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  

Вопросы также можно задавать на [форуме Stack Overflow в разделе, посвященном Azure Synapse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Чтобы запросить новую функцию, воспользуйтесь страницей [отзывов и предложений по Azure Synapse ](https://feedback.azure.com/forums/307516-sql-data-warehouse).  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.
