---
title: Передовые методы разработки для Synapse S'L
description: Рекомендации и рекомендации, которые вы должны знать при разработке synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429620"
---
# <a name="development-best-practices-for-synapse-sql"></a>Передовые методы разработки для Synapse S'L
В этой статье приводятся руководство и рекомендации по разработке решения хранилища данных. 

## <a name="development-best-practices-for-synapse-sql"></a>Передовые методы разработки для Synapse S'L

### <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения о сокращении затрат при помощи приостановки и масштабирования см. в статье об [управлении вычислительными ресурсами](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Обеспечение статистики

Убедитесь, что статистика обновляется ежедневно или после каждой загрузки.  Однако всегда есть компромиссы между производительностью и затратами на создание и обновление статистики. Если вы обнаружите, что для поддержания всех статистических данных требуется слишком много времени, будьте более избирательны в отношении того, какие столбцы имеют статистику или какие столбцы нуждаются в частом обновлении.  

Например, можно обновить столбцы дат, где новые значения могут добавляться ежедневно. **Наибольшую выгоду вы получите, имея статистику по столбикам, участвующим в соединениях, столбцам, используемым в пункте WHERE, и столбцам, найденным в GROUP BY.**

Смотрите также [Управление таблицей статистики](develop-tables-statistics.md), [CREATE STATISTICS](develop-tables-statistics.md), ОБНОВЛЕНИЕ [STATISTICS][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц

По умолчанию таблицы распределяются по методу циклического перебора.  Это упрощает для пользователей начало создавать таблицы без необходимости решать, как распределять их таблицы.  Круглые столы Робин может выполнять достаточно для некоторых рабочих нагрузок. Но в большинстве случаев выбор столбца распределения будет работать намного лучше.  

Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  Например, если у вас есть таблица заказов, которая распределяется order_id, и таблица транзакций, которая также распределяется order_id, при присоединении таблицы заказов к таблице транзакций order_id, этот запрос становится сквозным запросом. Это означает, что мы устраняем операции по перемещению данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.

При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  Приведенные ниже ссылки для получения дополнительной информации о том, как выбор столбца распределения может повысить производительность, а также как определить распределенную таблицу в пункте с предложением CREATE TABLES.

Смотрите также [Обзор таблицы][Table overview], [Распределение таблицы][Table distribution], [Выбор распределения таблицы][Selecting table distribution], [CREATE TABLE][CREATE TABLE], CREATE TABLE [AS SELECT.][CREATE TABLE AS SELECT]

### <a name="do-not-over-partition"></a>Недопущение избыточного секционирования
В то время как данные раздела могут быть эффективными для поддержания ваших данных путем переключения разделов или оптимизации сканирования с помощью исключения раздела, наличие слишком большого количества разделов может замедлить ваши запросы.  Часто стратегия раздела с высокой гранулированностью, которая может хорошо работать на сервере S'L, может не хорошо работать в пуле S'L.  

Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк. СЗЛ объединяет ваши данные для вас в 60 баз данных. Таким образом, если создать таблицу со 100 перегородками, результат составит 6000 разделов.  Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  

Одним из вариантов рассмотрения является использование детализации, которая ниже, чем то, что, возможно, работал для вас в сервере S'L.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

Смотрите также [раздел таблицы][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций

Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  Например, если у вас есть INSERT, который, как ожидается, займет 1 час, вы можете разбить INSERT на четыре части, тем самым сократив каждый запуск до 15 минут.

К пустым таблицам можно применять специальные операции, которые сопровождаются записью в журнал минимальных сведений, (такие как CTAS, TRUNCATE, DROP TABLE или INSERT), чтобы снизить риск отката.  Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, упорядоченной по идентификатору order_date (октябрь 2001 г.), данные можно секционировать ежемесячно, а потом переключить секцию с данными на пустую секцию из другой таблицы (см. примеры использования инструкции ALTER TABLE).  

Используя инструкцию CTAS вместо DELETE, можно записать данные, которые необходимо сохранить в несекционированной таблице.  Если на выполнение этих двух инструкций требуется одинаковое количество времени, целесообразно использовать инструкцию CTAS, так как для ее выполнения необходима минимальная нагрузка ведения журнала транзакций, и ее можно быстро отменить.

Смотрите также [Понимание транзакций][Understanding transactions], [Оптимизация транзакций][Optimizing transactions], [Раздел таблицы][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], ALTER [TABLE][ALTER TABLE], [Создать таблицу как выбрать (CTAS)][Create table as select (CTAS)].

### <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца

При определении DDL рекомендуется использовать поддерживаемый тип данных с наименьшим размером. Это позволит повысить производительность запросов.  Это особенно важно для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Table overview], [типам данных таблиц](develop-tables-data-types.md) и инструкции [CREATE TABLE][CREATE TABLE].

### <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore

Кластерные индексы столбцов являются одним из наиболее эффективных способов хранения данных в пуле S'L.  По умолчанию таблицы в пуле S'L создаются как кластерный columnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  

Качество сегмента можно изменить по числу строк в сжатой группе строк.  Дополнительные сведения об определении и улучшении качества сегмента для таблиц с кластеризованными индексами columnstore см. в разделе [Причины низкого качества индекса Columnstore][Causes of poor columnstore index quality] статьи [Индексирование таблиц в хранилище данных SQL][Table indexes].  Так как высококачественные сегменты columnstore важны, рекомендуется использовать идентификаторы пользователей, которым назначен класс ресурсов среднего или большого размера для загрузки данных. Использование [единиц использования хранилища данных](azure -synapse-resource-consumption-models.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Поскольку таблицы столбцов обычно не будут перемещать данные в сегмент сжатого столбцов до тех пор, пока на столе не будет более 1 миллиона строк, а каждый бильярдный стол S'L будет разделен на 60 таблиц, таблицы столбцов не приносят пользу запросу, если в таблице не будет более 60 миллионов строк.  Для таблиц с менее чем 60 миллионами строк, наличие индекса columstore не может быть оптимальным решением.  

Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица имеет 100 разделов, то она должна иметь по крайней мере 6 миллиардов строк, чтобы воспользоваться кластерными столбиками магазина (60 дистрибутивов *100 разделов* 1 миллион строк).  

Если в таблице нет 6 миллиардов строк, либо уменьшите количество разделов, либо рассмотрите возможность использования таблицы кучи.  Также может быть стоит поэкспериментировать, чтобы увидеть, можно ли повысить производительность с помощью таблицы кучи со вторичными индексами, а не таблицой столбцов.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Смотрите также [Таблица индексов][Table indexes], [Columnstore индексы руководства][Columnstore indexes guide], [Восстановление columnstore индексы][Rebuilding columnstore indexes].

### <a name="next-steps"></a>Дальнейшие действия

Если вы не нашли то, что искали в этой статье, попробуйте использовать «Поиск документов» в левой части этой страницы для поиска всех документов пула Azure S'L.  [Форум бассейнов Azure S'L][Azure SQL pool MSDN Forum] — это место, где вы задавайте вопросы другим пользователям и группе продуктов пула S'L.  

Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Если вы предпочитаете задавать свои вопросы по поводу переполнения стеков, у нас также есть [форум перелива стек Azure S'L.][Azure SQL pool Stack Overflow Forum]

Для запросов функций используйте страницу [обратной связи с пулом Azure S'L.][Azure SQL pool Feedback]  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Рекомендации по разработке для S'L по требованию (предварительный просмотр)

### <a name="general-considerations"></a>Общие рекомендации

По требованию S'L позволяет загоните файлы в учетных записях хранения Azure. Он не имеет возможности локального хранения или приема, что означает, что все файлы, которые цели запроса являются внешними для S'L по требованию. Таким образом, все, что связано с чтением файлов из хранилища, может повлиять на производительность запроса.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Переместите учетную запись хранения Azure и S'L по требованию

 
Чтобы свести к минимуму задержку, свиная учетная запись хранения Azure и конечная точка s'L по требованию. Учетные записи хранения данных и конечные точки, подготовленные при создании рабочего пространства, расположены в одном регионе. 
 
Для оптимальной производительности, если вы получаете доступ к другим учетным записям хранения данных с помощью S'L по требованию, убедитесь, что они находятся в том же регионе. В противном случае будет увеличена задержка передачи сети данных из удаленного региона в конечный район.

### <a name="azure-storage-throttling"></a>Регулирование хранения azure

Несколько приложений и служб могут получить доступ к вашей учетной записи хранения. Когда комбинированные IOPS или пропускная мощность, генерируемые приложениями, службами и рабочей нагрузкой по требованию, превышают пределы учетной записи хранилища, происходит регулирование хранилища. При задушии хранилища происходит существенное негативное влияние на производительность запроса. 
 
После обнаружения регулирования, S'L по требованию имеет встроенный в обработке этого сценария. По требованию S'L будет делать запросы на хранение более медленными темпами до тех пор, пока регулирование не будет решена. Однако при оптимальном выполнении запроса рекомендуется не подчеркивать учетную запись хранилища другими рабочими нагрузками во время выполнения запроса.

### <a name="prepare-files-for-querying"></a>Подготовка файлов к запросу

Если это возможно, вы можете подготовить файлы для повышения производительности:

- Преобразование CSV в паркет - Паркет является форматом columnar. Так как он сжат, он имеет меньшие размеры файлов, чем CSV файлов с теми же данными, и S'L по требованию потребуется меньше времени и хранения запросов, чтобы прочитать его.
- Если запрос нацелен на один большой файл, вы получите выгоду от его разделения на несколько небольших файлов.
- Старайтесь держать размер файла CSV ниже 10 ГБ.
- Предпочтительно иметь файлы одинакового размера для одного пути OPENROWSET или внешней таблицы LOCATION.
- Разделив данные путем хранения разделов на разные папки или имена файлов - проверьте [использование файловых имен и функций файлового пути для целевой целевой специфии.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Используйте функции fileinfo и filepath для таргетинга на конкретные разделы

Данные часто организуются в разделах. Вы можете поручить S'L по требованию задать запрос определенных папок и файлов. Это уменьшит количество файлов и объем данных, необходимых для чтения и обработки запроса. Следовательно, вы достигнете лучшей производительности. Для получения дополнительной информации проверьте [функции имени файла](develop-storage-files-overview.md#filename-function) и [файла](develop-storage-files-overview.md#filepath-function) и примеры того, как [запросить определенные файлы.](query-specific-files.md)

Если данные в хранилище не разделены, подумайте о его разделении, чтобы можно было использовать эти функции для оптимизации запросов, ориентированных на эти файлы.

При [запросе разделенных таблиц Spark](develop-storage-files-spark-tables.md) из таблиц S'L по требованию запрос автоматически будет нацелен только на необходимые файлы.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Используйте CETAS для повышения производительности запроса и соединения

[CETAS](develop-tables-cetas.md) является одной из наиболее важных функций, доступных по требованию. CETAS — это параллельная операция, которая создает внешние метаданные таблицы и экспортирует результат запроса SELECT в набор файлов в учетной записи хранилища.

Можно использовать CETAS для хранения часто используемых частей запросов, таких как объединенные справочные таблицы, в новый набор файлов. Позже можно присоединиться к этой единой внешней таблице вместо повторения общих соединений в нескольких запросах. По мере того как CETAS генерирует файлы паркета, статистика будет автоматически создаваться, когда первый запрос нацелен на эту внешнюю таблицу, и вы получите улучшенную производительность.
