---
title: Рекомендации по использованию пула SQL синапсе в Azure синапсе Analytics (прежнее название — SQL DW)
description: Рекомендации и рекомендации по разработке решений для пула SQL в Azure синапсе Analytics (ранее — хранилище данных SQL).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4462bf0fc2057922340eb01cb8c786dbc63ce290
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745355"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Рекомендации по использованию пула SQL синапсе в Azure синапсе Analytics (прежнее название — SQL DW)

Эта статья представляет собой набор рекомендаций, которые помогут добиться оптимальной производительности при развертывании [пула SQL](sql-data-warehouse-overview-what-is.md) .  Цель этой статьи — предоставить вам основные рекомендации и выделить важные области фокуса.  

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения о сокращении затрат при помощи приостановки и масштабирования см. в статье об [управлении вычислительными ресурсами](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Обеспечение статистики

Пул SQL можно настроить для автоматического обнаружения и создания статистики по столбцам.  Планы запросов, созданные оптимизатором, так же хороши, как и доступные статистические данные.  

Рекомендуется включить AUTO_CREATE_STATISTICS для баз данных и обновлять статистику ежедневно или после каждой нагрузки, чтобы гарантировать, что статистика по столбцам, используемых в запросах, всегда будет актуальной.

Если вы обнаружите, что обновление всей статистики занимает слишком много времени, может возникнуть необходимость более избирательно выбирать столбцы, для которых требуется часто обновлять статистику. Например, можно ежедневно обновлять столбцы дат, в которые добавляются новые значения.

> [!TIP]
> Вы получите наибольшее преимущество, используя обновленную статистику по столбцам, участвующим в соединении, столбцам, используемым в предложении WHERE и столбцах, найденных в поле GROUP BY.

См. также раздел [Управление статистикой таблицы](sql-data-warehouse-tables-statistics.md), [Создание статистики](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)и [Обновление статистики](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов

В пуле SQL имеется несколько динамических административных представлений, которые можно использовать для наблюдения за выполнением запросов.  Пошаговые инструкции по просмотру сведений о выполняемом запросе см. в статье мониторинг рабочей нагрузки с помощью динамических административных представлений.  

Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL.

См. также [мониторинг рабочей нагрузки с помощью динамических административных представлений](sql-data-warehouse-manage-monitor.md), [метки](sql-data-warehouse-develop-label.md), [параметра](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)и [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Настройка производительности запросов с помощью новых усовершенствований продукта

- [Performance tuning with materialized views](performance-tuning-materialized-views.md) (Настройка производительности с помощью материализованных представлений)
- [Performance tuning with result set caching](performance-tuning-ordered-cci.md) (Настройка производительности с помощью упорядоченного кластеризованного индекса columnstore)
- [Performance tuning with result set caching](performance-tuning-result-set-caching.md) (Настройка производительности путем кэширования результирующего набора)

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы

Однократная загрузка небольшой таблицы с инструкцией INSERT или даже периодическая повторная загрузка подстановки может быть хорошо выполнена для ваших потребностей с помощью оператора, такого как `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  

Однако для загрузки тысяч или миллионов строк на протяжении дня одноэлементных инструкций INSERT может быть недостаточно.  Вместо них можно создать процессы, которые будут записывать инструкции INSERT в файл и периодически загружать его.

См. также [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

Пул SQL поддерживает загрузку и экспорт данных с помощью нескольких средств, включая фабрику данных Azure, Polybase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  Однако для загрузки или экспорта данных большого объема, что требует высокой производительности, лучше всего использовать PolyBase.  

Polybase предназначен для использования архитектуры MPP (с массовой параллельной обработкой), а также для загрузки и экспорта данных, которые будут выполняться быстрее любого другого средства.  Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO.  

> [!TIP]
> Команда CTAS позволяет свести к минимуму нагрузку ведения журнала транзакций и быстрее всего выполнить загрузку данных.

Фабрика данных Azure также поддерживает нагрузки PolyBase и способна достичь производительности, сопоставимой с CTAS.  PolyBase поддерживает различные форматы файлов, включая формат GZIP.  
  
> [!NOTE]
> Чтобы повысить пропускную способность при использовании текстовых файлов gzip, разбейте файлы на 60 или более файлов, чтобы максимально увеличить параллелизм нагрузки.  Кроме того, для повышения общей пропускной способности можно загружать данные одновременно.

См. также [Загрузка данных](design-elt-data-loading.md), [руководств по использованию polybase](guidance-for-loading-data.md), [шаблонов загрузки пула SQL и стратегий](https://blogs.msdn.microsoft.com/sqlcat/20../../), [Загрузка данных с помощью фабрики данных]( ../../data-factory/load-azure-sql-data-warehouse.md)Azure, [Перемещение данных с помощью фабрики данных Azure](../../data-factory/transform-data-using-machine-learning.md), [Создание формата внешнего файла](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)и [Создание таблицы как SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним

Технология Polybase (т. н. внешние таблицы), возможно, является самым быстрым способом загрузки данных, но она не оптимальна с точки зрения запросов. В настоящее время таблицы polybase поддерживают только файлы BLOB-объектов Azure и хранилище Azure Data Lake. Эти файлы не обслуживаются какими-либо вычислительными ресурсами.  

В результате пул SQL не может разгрузить эту работу, поэтому необходимо прочитать весь файл, загрузив его в базу данных tempdb для чтения данных.  Таким образом, если будет отправлено несколько запросов к этим данным, то лучше один раз загрузить их в локальную таблицу и предоставить ее для выполнения запросов.

См. также инструкции по [использованию polybase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц

По умолчанию таблицы распределяются по методу циклического перебора.  Это позволяет упростить процесс создания таблиц, так как пользователям не нужно принимать решение о типе распределения.  Таблицы с распределением по методу циклического перебора могут вполне годиться для некоторых рабочих нагрузок, но в большинстве случаев намного эффективнее использовать столбец распределения.  

Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  

Например, во время выполнения запроса на объединение таблицы Orders, распределенной по идентификатору order_id, с таблицей Transactions, распределенной по тому же идентификатору, этот запрос превращается в запрос к серверу, что исключает выполнение операций перемещения данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.  

> [!TIP]
> При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  

Дополнительные сведения о том, как выбор столбца распределения может повысить производительность, а также определение распределенной таблицы в предложении WITH инструкции CREATE TABLE, см. по следующим ссылкам.

См. также [Обзор таблиц](sql-data-warehouse-tables-overview.md), [распределение таблиц](sql-data-warehouse-tables-distribute.md), [Выбор распределения ТАБЛИЦ](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE как SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования

В то время как секционирование данных может быть эффективным для поддержания данных посредством переключения секций или оптимизации проверок с помощью исключения секций, наличие слишком большого количества секций может замедлить выполнение запросов.  Часто это стратегия секционирования с высокой степенью детализации, которая хорошо работает с SQL Server может плохо работать в пуле SQL.  

Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк.  Помните, что в фоновом режиме пул SQL разделяет данные на базы данных 60, поэтому при создании таблицы с 100 секций это приводит к 6000 секций.  

Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  В хранилище данных SQL можно использовать меньшее количество секций, чем в SQL Server.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

См. также [секционирование таблиц](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций

Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  

Например, если имеется вставка, которая займет 1 час, по возможности Разбейте вставку на четыре части, которые будут выполняться в течение 15 минут.  Для снижения риска отката используйте специальные случаи минимального протоколирования, такие как CTAS, УСЕЧЕНИЕ, удаление таблицы или Вставка в пустые таблицы.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, где order_date в октябре 2001, можно ежемесячно секционировать данные, а затем переключать секцию данными для пустых секций из другой таблицы (см. раздел инструкции по [изменению таблицы](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ).  

Для несекционированных таблиц рекомендуется использовать CTAS для записи данных, которые необходимо сохранить в таблице, а не использовать DELETE.  Если CTAS занимает то же время, это намного безопасная работа, так как она имеет минимальное протоколирование транзакций и при необходимости может быть отменена.

См. также раздел [Основные сведения о транзакциях](sql-data-warehouse-develop-transactions.md), [Оптимизация транзакций](sql-data-warehouse-develop-best-practices-transactions.md), [секционирование ТАБЛИЦ](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), а [также создание таблицы как SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Уменьшение размеров результатов запроса

Этот шаг позволяет избежать проблем на стороне клиента, вызванных большим результатом запроса.  Можно изменить запрос, чтобы уменьшить число возвращаемых строк. Некоторые средства создания запросов позволяют добавлять в каждый запрос синтаксис "Top N".  Можно также CETAS результат запроса во временную таблицу, а затем использовать экспорт Polybase для обработки прежних версий.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца

При определении DDL с использованием наименьшего типа данных, который будет поддерживать ваши данные, повышает производительность запросов.  Этот подход особенно важен для столбцов CHAR и VARCHAR.  

Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

См. также [Общие сведения о таблице](sql-data-warehouse-tables-overview.md), [табличные типы данных](sql-data-warehouse-tables-data-types.md) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных

При временном размещении данных может оказаться, что использование таблицы кучи сделает общий процесс быстрее.  Если вы загружаете данные только для промежуточного хранения перед их последующими преобразованиями, загрузка таблицы в таблицу кучи будет происходить значительно быстрее по сравнению с загрузкой данных в кластеризованную таблицу columnstore.  

Кроме того, загрузка данных во временную таблицу выполняется гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с "#" и доступны только создавшему его сеансу, поэтому они могут работать только в ограниченных сценариях.

Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

См. также [временные таблицы](sql-data-warehouse-tables-temporary.md) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE в качестве SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore

Кластеризованные индексы columnstore — это один из наиболее эффективных способов хранения данных в пуле SQL.  По умолчанию таблицы в пуле SQL создаются как кластеризованный ColumnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  

Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  Качество сегмента можно изменить по числу строк в сжатой группе строк.  Пошаговые инструкции по обнаружению и улучшению качества сегментов для кластеризованных таблиц columnstore см. в статьях [причины низкого качества индексов columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) в статье [индексы таблиц](sql-data-warehouse-tables-index.md) .  

Так как высококачественные сегменты columnstore важны, рекомендуется использовать идентификаторы пользователей, которые находятся в классе ресурсов среднего или большого размера для загрузки данных. Использование [единиц использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Так как таблицы columnstore обычно не отправляют данные в сжатый сегмент columnstore до тех пор, пока в таблице не будет более 1 000 000 строк, а каждая таблица пула SQL секционирована на 60 таблицы, в качестве правила Thumb таблицы columnstore не смогут получить запрос, если только таблица не содержит более 60 000 000 строк.  Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Для таблицы с количеством строк менее 60 миллионов бессмысленно использовать индекс Columnstore, хотя он и не повлияет на производительность.  

Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица содержит 100 секций, то для получения преимуществ из хранилища кластеризованных столбцов требуется по крайней мере 6 000 000 000 строк (60 распределений *100 секции* 1 000 000 строки).  

Если таблица не содержит такого количества строк, рекомендуется уменьшить количество секций или использовать таблицу без кластеризованных индексов.  Чтобы получить более высокую производительность, возможно, вместо кластеризованной таблицы стоит использовать таблицу без кластеризованных индексов, содержащую вторичные индексы.

> [!TIP]
> Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Ознакомьтесь также со статьями [Индексирование таблиц в хранилище данных SQL](sql-data-warehouse-tables-index.md), [Руководство по индексам columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и разделом с описанием [повторной сборки индексов columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов

Пул SQL использует группы ресурсов в качестве способа выделения памяти для запросов.  В этом случае всем пользователям назначается небольшой класс ресурсов, который предоставляет 100 МБ памяти для каждого распределения.  В хранилище данных SQL содержится 60 распределений, на каждое из которых выделяется как минимум 100 МБ памяти. Поэтому в целом для системы выделяется 6000 МБ памяти (около 6 ГБ).  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  Некоторые запросы, например чистые проверки, не дают никаких преимуществ.  Однако использование больших классов ресурсов сокращает параллелизм, поэтому необходимо учитывать это воздействие перед перемещением всех пользователей в большой класс ресурсов.

См. также [классы ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Использование класса ресурсов небольшого размера для увеличения параллелизма

Если вы заметили, что запросы пользователей кажутся длительными, это может быть вызвано тем, что ваши пользователи работают в больших классах ресурсов и потребляют много слотов выдачи, в результате чего другие запросы помещаются в очередь.  Чтобы посмотреть очередь выполнения пользовательских запросов, выполните команду `SELECT * FROM sys.dm_pdw_waits`, которая позволяет просмотреть возвращенные строки.

См. также [классы ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md), [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Другие ресурсы

Дополнительные сведения о распространенных проблемах и решениях см. в статье [Устранение неполадок хранилища данных SQL Azure](sql-data-warehouse-troubleshoot.md).

Если вы не нашли то, что ищете в этой статье, воспользуйтесь командой "Поиск документов" в левой части этой страницы, чтобы найти все документы Azure синапсе.  [Форум Azure синапсе](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) — это место для отправки вопросов другим пользователям и группе продуктов Azure синапсе. Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  

Если вы предпочитаете задавать вопросы на Stack Overflow, у нас также есть [форум по Azure синапсе Stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Используйте страницу [обратной связи Azure синапсе](https://feedback.azure.com/forums/307516-sql-data-warehouse) для выполнения запросов на функции.  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.
