---
title: Рекомендации по использованию пулов SQL
description: Рекомендации и рекомендации, которые следует учитывать при работе с пулами SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427800"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Рекомендации по использованию пулов SQL в Azure синапсе Analytics

Эта статья содержит набор рекомендаций, которые помогут добиться оптимальной производительности для пулов SQL в Azure синапсе Analytics. Ниже вы найдете основные рекомендации и важные области, которые помогут вам сосредоточиться на создании решения. В каждом разделе рассказывается о концепции, а затем указываются более подробные статьи, в которых более подробно рассматривается концепция.

## <a name="sql-pools-loading"></a>Загрузка пулов SQL

Инструкции по загрузке [данных](data-loading-best-practices.md)для пулов SQL см. в этой статье.

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения об уменьшении затрат путем приостановки и масштабирования см. в разделе [Управление вычислениями](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Обеспечение статистики

Хотя SQL Server автоматически обнаруживает и создает или обновляет статистику по столбцам, для пулов SQL требуется ручное обслуживание статистики. Необходимо сохранить статистику, чтобы обеспечить оптимизацию планов пула SQL.  Планы, созданные с помощью оптимизатора, зависят от доступной статистики.

> [!TIP]
> Проще всего приступить к работе со статистикой, создав статистику для каждого столбца.  

Не менее важно также обновлять статистику, так как данные могут быть существенно изменены.  Рекомендуется выполнять обновление статистики ежедневно или после каждой загрузки.  Однако всегда есть компромиссы между производительностью и затратами на создание и обновление статистики.

Чтобы сократить время обслуживания статистики, необходимо указать, какие столбцы имеют статистику или требуют наиболее частых обновлений. Например, может потребоваться обновить столбцы даты, где можно добавлять новые значения ежедневно. Сосредоточьтесь на получении статистики по столбцам, задействованным в объединениях, столбцах, используемых в предложении WHERE, и столбцах, найденных в GROUP BY.

Дополнительные сведения о статистике можно найти в статьях [Управление статистикой таблицы](develop-tables-statistics.md), [Создание статистики](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)и [Обновление статистики](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы

Однократная загрузка небольшой таблицы с инструкцией INSERT, например, `INSERT INTO MyLookup VALUES (1, 'Type 1')`может быть лучшим решением в зависимости от ваших потребностей. Однако если в течение дня необходимо загружать тысячи или миллионы строк, скорее всего, одноэлементные вставки не являются оптимальными.

Одним из способов решения этой проблемы является разработка одного процесса, записывающего в файл, а затем другого процесса для периодической загрузки этого файла. Дополнительные сведения см. в статье [Вставка](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) статьи.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

Пул SQL поддерживает загрузку и экспорт данных с помощью нескольких средств, включая фабрику данных Azure, Polybase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  

> [!NOTE]
> Polybase является лучшим выбором при загрузке или экспорте больших объемов данных или при необходимости повышения производительности.

Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO. CTAS будет сокращать ведение журнала транзакций и является самым быстрым способом загрузки данных. Фабрика данных Azure также поддерживает загрузку Polybase и может обеспечить производительность, аналогичную CTAS. Polybase поддерживает различные форматы файлов, включая файлы gzip.

Чтобы повысить пропускную способность при использовании текстовых файлов gzip, разбейте файлы на 60 или более файлов, чтобы максимально увеличить параллелизм нагрузки. Кроме того, для повышения общей пропускной способности можно загружать данные одновременно. Дополнительные сведения о разделах, относящихся к этому разделу, приведены в следующих статьях:

- [Загрузка данных](data-loading-overview.md)
- [руководством по использованию PolyBase](data-loading-best-practices.md)
- [Шаблоны и стратегии загрузки пула SQL Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Загрузка данных с помощью фабрики данных Azure](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Перенос данных с помощью фабрики данных Azure](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Создать таблицу как SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним

Polybase не оптимально подходит для запросов. Таблицы polybase для пулов SQL в настоящее время поддерживают только файлы больших двоичных объектов Azure и хранилище Azure Data Lake. Эти файлы не имеют никаких ресурсов вычислений. В результате пулы SQL не могут отгружать эту работу и считывать весь файл, загружая его в базу данных tempdb, чтобы он мог считывать данные.

Если у вас есть несколько запросов для запроса этих данных, лучше загрузить эти данные один раз и использовать в запросах локальную таблицу. Дополнительные рекомендации по polybase приведены в руководстве по [использованию статьи polybase](data-loading-best-practices.md) .

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц

По умолчанию таблицы распределяются по методу циклического перебора.   Это значение по умолчанию позволяет пользователям легко начать создавать таблицы, не принимая решение о том, как следует распределять их таблицы. Таблицы с циклическим перебором могут работать достаточно эффективно для некоторых рабочих нагрузок. Но в большинстве случаев столбец распределения обеспечивает лучшую производительность.  

Наиболее распространенным примером таблицы, распределенной по столбцу, выполняющему циклическую таблицу, является соединение двух больших таблиц фактов.  

Например, если имеется таблица Orders, распределенная по order_idю, и таблица Transactions, которая также распространяется с помощью order_id, при присоединении таблицы Orders к таблице Transactions на order_id этот запрос станет передаваемым запросом. Затем операции перемещения данных удаляются. Чем меньше в запросе действий, тем быстрее он выполняется. Скорость выполнения запроса также зависит от объема перемещаемых данных.

> [!NOTE]
> При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения. Это приведет к снижению нагрузки.

Приведенные ниже ссылки на статьи помогут вам получить дополнительные сведения о повышении производительности с помощью выбора столбца распределения. Кроме того, вы найдете сведения о том, как определить распределенную таблицу в предложении WITH оператора CREATE TABLE:

- [Обзор таблицы](develop-tables-overview.md)
- [Распределение таблиц](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Выбор распределения таблицы](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования

В то время как секционирование данных может быть эффективным для поддержания данных посредством переключения секций или оптимизации проверок с помощью исключения секций, наличие слишком большого количества секций может замедлить выполнение запросов.  Зачастую стратегия секционирования с высоким уровнем детализации, которая хорошо работает на SQL Server может работать не в пуле SQL.  

Наличие слишком большого количества секций может снизить эффективность кластеризованных индексов columnstore, если в каждой секции менее 1 000 000 строк. Пулы SQL автоматически разделяют данные на базы данных 60. Таким образом, если создается таблица с 100 секциями, результат будет составлять 6000 секций. Каждая рабочая нагрузка отличается, поэтому лучше поэкспериментировать с секционированием, чтобы узнать, что лучше подходит для вашей рабочей нагрузки.  

Одним из вариантов является использование гранулярности, которая ниже, чем вы реализовали с помощью SQL Server. Например, вместо ежедневных секций рекомендуется использовать еженедельные или ежемесячные разделы.

Дополнительные сведения о секционировании подробно описаны в статье [секционирование таблиц](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций

Инструкции INSERT, UPDATE и DELETE выполняются в транзакции. В случае сбоя они должны быть откатываются. Чтобы уменьшить вероятность длительного отката, по возможности сократите размер транзакций.  Свести к минимуму размер транзакций можно, разделив операторы вставки, обновления и удаления на части. Например, если имеется вставка, которая займет 1 час, можно разбить вставку на четыре части. Каждый запуск будет сокращен до 15 минут.  

> [!TIP]
> Используйте специальные минимальные случаи ведения журнала, например CTAS, TRUNCATE, DROP TABLE или INSERT в пустые таблицы, чтобы снизить риск отката.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, в которой order_date в октябре 2001, данные можно секционировать ежемесячно. Затем можно отключить секцию данными для пустой секции из другой таблицы (см. раздел ALTER TABLE examples).  

Для несекционированных таблиц рекомендуется использовать CTAS для записи данных, которые необходимо сохранить в таблице, а не использовать DELETE.  Если CTAS занимает то же время, его выполнение значительно безопаснее, так как оно имеет минимальное протоколирование транзакций и при необходимости может быть отменено быстро.

Дополнительные сведения о содержимом, связанном с этим разделом, приведены в следующих статьях:

- [Создать таблицу как SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Основные сведения о транзакциях](develop-transactions.md)
- [Оптимизация транзакций](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Секционирование таблиц в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ИЗМЕНЕНИЕ ТАБЛИЦЫ](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Уменьшение размеров результатов запроса

Уменьшение размера результатов запроса помогает избежать проблем на стороне клиента, вызванных большими результатами запросов.  Можно изменить запрос, чтобы уменьшить число возвращаемых строк. Некоторые средства создания запросов позволяют добавлять в каждый запрос синтаксис "Top N".  Можно также CETAS результат запроса во временную таблицу, а затем использовать экспорт Polybase для обработки прежних версий.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца

При определении DDL используйте наименьший тип данных, который будет поддерживать ваши данные, что позволит повысить производительность запросов.  Эта рекомендация особенно важна для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, следует определить столбцы как VARCHAR, если это все, что необходимо, а не использовать NVARCHAR.

Дополнительные сведения о важных концепциях, связанных с приведенной выше информацией, см. в [обзоре таблицы](develop-tables-overview.md), [табличных типах данных](develop-tables-data-types.md)и [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) статьях.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных

При временном размещении данных в пулах SQL таблицы кучи обычно делают общий процесс быстрее.  Если данные загружаются только для промежуточного размещения перед выполнением дополнительных преобразований, Загрузка таблицы в таблицу кучи будет выполняться быстрее, чем загрузка данных в кластеризованную таблицу columnstore.  

Загрузка данных во временную таблицу также будет загружаться намного быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с "#" и доступны только создавшему его сеансу. Следовательно, они могут работать только в ограниченных сценариях. Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Дополнительные рекомендации см. [во временных таблицах](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)и [CREATE TABLE в качестве выбранных](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) статей.

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore

Кластеризованные индексы columnstore — это один из наиболее эффективных способов хранения данных в пуле SQL.  По умолчанию таблицы в пуле SQL создаются как кластеризованный ColumnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  

Качество сегментов может измеряться по количеству строк в сжатой группе строк. Пошаговые инструкции по обнаружению и улучшению качества сегментов для кластеризованных таблиц columnstore см. в статьях [причины низкого качества индексов columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) в статье [индексы таблиц](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .  

Так как высококачественные сегменты columnstore важны, рекомендуется использовать идентификаторы пользователей, которые находятся в классе ресурсов среднего или большого размера для загрузки данных. Использование [единиц использования хранилища данных](resource-consumption-models.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Таблицы columnstore обычно не отправляют данные в сжатый сегмент columnstore до тех пор, пока в таблице не будет более 1 000 000 строк. Каждая таблица пула SQL разделяется на таблицы 60. Таким образом, таблицы columnstore не смогут использовать запрос, если таблица содержит более 60 000 000 строк.  

> [!TIP]
> Для таблиц, в которых менее 60 000 000 строк, наличие индекса columnstore может быть не оптимальным решением.  

При секционировании данных каждой секции потребуется 1 000 000 строк, чтобы получить преимущество от кластеризованного индекса columnstore.  Для таблицы с 100 секциями необходимо иметь по крайней мере 6 000 000 000 строк, чтобы использовать преимущества хранилища кластеризованных столбцов (60 распределений *100 секции* 1 000 000 строк).  

Если таблица не содержит 6 000 000 000 строк, у вас есть два основных варианта. Либо Сократите количество секций, либо рассмотрите возможность использования таблицы кучи.  Также может потребоваться эксперимент, чтобы узнать, можно ли повысить производительность с помощью таблицы кучи с вторичными индексами, а не с таблицей columnstore.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  Дополнительные сведения о таблицах и индексах columnstore. их можно найти в статьях [индексы таблиц](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [индексы columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)и [перестроить статьи индексов columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) .

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов

Пулы SQL используют группы ресурсов в качестве способа выделения памяти для запросов. Изначально все пользователи назначаются небольшому классу ресурсов, который предоставляет 100 МБ памяти на распределение.  Всегда существует 60 дистрибутивов. Каждому дистрибутиву предоставляется минимум 100 МБ. Общий объем памяти, выделяемой для всей системы, составляет 6 000 МБ или только менее 6 ГБ.  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  Некоторые запросы, например чистые проверки, не смогут получить никаких преимуществ. Использование больших классов ресурсов влияет на параллелизм. Поэтому перед перемещением всех пользователей в большой класс ресурсов необходимо помнить об этих фактах.

Дополнительные сведения о классах ресурсов см. в статье [классы ресурсов для управления рабочей нагрузкой](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Используйте класс ресурсов меньшего размера для увеличения параллелизма

Если вы заметите длительную задержку в запросах пользователя, пользователи могут работать в больших классах ресурсов. Этот сценарий повышает потребление слотов выдачи, что может привести к постановке других запросов в очередь.  Чтобы определить, были ли запросы пользователей поставлены в `SELECT * FROM sys.dm_pdw_waits` очередь, выполните команду, чтобы проверить, возвращаются ли какие бы то ни было строки.

В статьях [классы ресурсов для управления рабочей нагрузкой](../sql-data-warehouse/resource-classes-for-workload-management.md) и статьи [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) предоставит дополнительные сведения.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов

Пулы SQL имеют несколько динамических административных представлений, которые можно использовать для наблюдения за выполнением запросов.  В статье о мониторинге приведены пошаговые инструкции по просмотру сведений о выполняемом запросе.  Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL. Дополнительные сведения см. в статьях, перечисленных в списке ниже.

- [Мониторинг рабочей нагрузки с помощью динамических административных представлений](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [ЗАГОЛОВКА](develop-label.md)
- [ФУНКЦИЮ](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Дальнейшие шаги

Общие проблемы и решения см. в статье [Устранение неполадок](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

Если вам нужна информация, не указанная в этой статье, используйте команду "Поиск документов" в левой части этой страницы, чтобы найти все документы пула SQL.  [Форум пула SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) — это место, где можно создавать вопросы для других пользователей и группы разработчиков пула SQL.  

Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Если вы предпочитаете задавать вопросы на Stack Overflow, у нас также есть [пул Azure SQL Stack overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Для запросов функций используйте страницу [отзывов о пуле SQL Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Добавление запросов или других запросов помогает нам сосредоточиться на наиболее востребованных функциях.
