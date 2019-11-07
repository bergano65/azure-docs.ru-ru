---
title: Рекомендации по SQL Analytics в Azure синапсе Analytics (прежнее название — SQL DW)
description: Рекомендации и рекомендации по разработке решений для аналитики SQL в Azure синапсе Analytics (ранее — хранилище данных SQL).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646180"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Рекомендации по SQL Analytics в Azure синапсе Analytics (прежнее название — SQL DW)

Эта статья представляет собой набор рекомендаций, которые помогут добиться оптимальной производительности при развертывании [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) .  Цель этой статьи — предоставить вам основные рекомендации и выделить важные области фокуса.  В каждом разделе рассказывается о концепции, а затем указываются более подробные статьи, в которых более подробно рассматривается концепция. Последовательность разделов находится в порядке важности. 

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения о сокращении затрат при помощи приостановки и масштабирования см. в статье об [управлении вычислительными ресурсами](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Обеспечение статистики
Хранилище данных SQL Azure можно настроить для автоматического обнаружения и создания статистики по столбцам.  Планы запросов, созданные оптимизатором, так же хороши, как и доступные статистические данные.  Рекомендуется включить AUTO_CREATE_STATISTICS для баз данных и обновлять статистику ежедневно или после каждой нагрузки, чтобы гарантировать актуальность статистики по столбцам, используемым в запросах. 

Если вы обнаружите, что обновление всей статистики занимает слишком много времени, может возникнуть необходимость более избирательно выбирать столбцы, для которых требуется часто обновлять статистику. Например, можно ежедневно обновлять столбцы дат, в которые добавляются новые значения. **Вы получите наибольшее преимущество, используя обновленную статистику по столбцам, участвующим в соединении, столбцам, используемым в предложении WHERE и столбцах, найденных в поле GROUP BY.**

Дополнительные сведения см. в статьях [Управление статистикой таблиц в хранилище данных SQL][Manage table statistics], [CREATE STATISTICS (Transact-SQL)][CREATE STATISTICS] и [UPDATE STATISTICS (Transact-SQL)][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов
В SQL Analytics имеется несколько динамических административных представлений, которые можно использовать для наблюдения за выполнением запросов.  В первой статье в списке ниже предоставлены пошаговые инструкции по отслеживанию выполнения запроса.  Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL.

Дополнительные сведения см. в статьях [Мониторинг рабочей нагрузки с помощью динамических административных представлений][Monitor your workload using DMVs], [Использование меток для инструментирования запросов в хранилище данных SQL][LABEL], [Предложение OPTION (Transact-SQL)][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] и [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Настройка производительности запросов с помощью новых усовершенствований продукта
- [Настройка производительности с материализованными представлениями](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Настройка производительности с упорядоченным кластеризованным индексом columnstore](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Настройка производительности с кэшированием результирующего набора](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы
Одноразовую загрузку в небольшую таблицу или даже периодическую перезагрузку результатов поиска рекомендуется выполнять, используя такой синтаксис инструкции INSERT: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Однако для загрузки тысяч или миллионов строк на протяжении дня одноэлементных инструкций INSERT может быть недостаточно.  Вместо них можно создать процессы, которые будут записывать инструкции INSERT в файл и периодически загружать его.

Дополнительные сведения см. в статье [Инструкция INSERT (Transact-SQL)][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

 SQL Analytics поддерживает загрузку и экспорт данных с помощью нескольких средств, включая фабрику данных Azure, Polybase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  Однако для загрузки или экспорта данных большого объема, что требует высокой производительности, лучше всего использовать PolyBase.  
 
 Polybase предназначен для использования архитектуры MPP (с массовой параллельной обработкой), а также для загрузки и экспорта данных, которые будут выполняться быстрее любого другого средства.  Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO.  **Команда CTAS позволяет свести к минимуму нагрузку ведения журнала транзакций и быстрее всего выполнить загрузку данных.** 
 
  Фабрика данных Azure также поддерживает нагрузки PolyBase и способна достичь производительности, сопоставимой с CTAS.  PolyBase поддерживает различные форматы файлов, включая формат GZIP.  **Чтобы повысить пропускную способность при использовании текстовых файлов gzip, разбейте файлы на 60 или более файлов, чтобы максимально увеличить параллелизм нагрузки.**  Кроме того, для повышения общей пропускной способности можно загружать данные одновременно.

См. также [Загрузка данных][Load data], [руководств по использованию polybase][Guide for using PolyBase], [шаблонов загрузки пула SQL и стратегий][Azure SQL Data Warehouse loading patterns and strategies], [загрузки данных с помощью фабрики данных][Load Data with Azure Data Factory]Azure, [перемещения данных с помощью фабрики данных Azure][Move data with Azure Data Factory], [создания формата внешнего файла][CREATE EXTERNAL FILE FORMAT], [создания Таблица как SELECT (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним
Технология Polybase (т. н. внешние таблицы), возможно, является самым быстрым способом загрузки данных, но она не оптимальна с точки зрения запросов. В настоящее время таблицы polybase поддерживают только файлы BLOB-объектов Azure и хранилище Azure Data Lake. Эти файлы не обслуживаются какими-либо вычислительными ресурсами.  

В результате SQL Analytics не может разгрузить эту работу, поэтому необходимо прочитать весь файл, загрузив его в базу данных tempdb для чтения данных.  Таким образом, если будет отправлено несколько запросов к этим данным, то лучше один раз загрузить их в локальную таблицу и предоставить ее для выполнения запросов.

Ознакомьтесь также с [руководством по использованию PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц
По умолчанию таблицы распределяются по методу циклического перебора.  Это позволяет упростить процесс создания таблиц, так как пользователям не нужно принимать решение о типе распределения.  Таблицы с распределением по методу циклического перебора могут вполне годиться для некоторых рабочих нагрузок, но в большинстве случаев намного эффективнее использовать столбец распределения.  Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  

Например, во время выполнения запроса на объединение таблицы Orders, распределенной по идентификатору order_id, с таблицей Transactions, распределенной по тому же идентификатору, этот запрос превращается в запрос к серверу, что исключает выполнение операций перемещения данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.  

При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  Дополнительные сведения о том, как выбор столбца распределения может повысить производительность, а также определение распределенной таблицы в предложении WITH инструкции CREATE TABLE, см. в следующих ссылках.

Дополнительные сведения см. в статьях [Общие сведения о таблицах в хранилище данных SQL][Table overview], [Распределение таблиц в хранилище данных SQL][Table distribution], а также в статьях, посвященных [выбору распределения таблицы][Selecting table distribution], [инструкции CREATE TABLE][CREATE TABLE] и [инструкции CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования
В то время как секционирование данных может быть эффективным для поддержания данных посредством переключения секций или оптимизации проверок с помощью исключения секций, наличие слишком большого количества секций может замедлить выполнение запросов.  Часто это стратегия секционирования с высокой степенью детализации, которая хорошо работает с SQL Server может плохо работать в SQL Analytics.  

Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк.  Помните, что в этом случае SQL Analytics разделяет ваши данные на базы данных 60, поэтому при создании таблицы с 100 секций это приводит к 6000 секций.  

Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  В хранилище данных SQL можно использовать меньшее количество секций, чем в SQL Server.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций
Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  

Например, если имеется вставка, которая займет 1 час, по возможности Разбейте вставку на четыре части, которые будут выполняться в течение 15 минут.  К пустым таблицам можно применять специальные операции, которые сопровождаются записью в журнал минимальных сведений, (такие как CTAS, TRUNCATE, DROP TABLE или INSERT), чтобы снизить риск отката.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, упорядоченной по идентификатору order_date (октябрь 2001 г.), данные можно секционировать ежемесячно, а потом переключить секцию с данными на пустую секцию из другой таблицы (см. примеры использования инструкции ALTER TABLE).  

Для несекционированных таблиц рекомендуется использовать CTAS для записи данных, которые необходимо сохранить в таблице, а не использовать DELETE.  Если CTAS занимает то же время, это намного безопасная работа, так как она имеет минимальное протоколирование транзакций и при необходимости может быть отменена.

Дополнительные сведения см. в статьях [Транзакции в хранилище данных SQL][Understanding transactions], [Оптимизация транзакций для хранилища данных SQL][Optimizing transactions], [Секционирование таблиц в хранилище данных SQL][Table partitioning], а также в статьях об инструкциях [TRUNCATE TABLE][TRUNCATE TABLE] и [ALTER TABLE][ALTER TABLE] и статье [Функция Create Table As Select (CTAS) в хранилище данных SQL][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Уменьшение размеров результатов запроса  
Этот шаг позволяет избежать проблем на стороне клиента, вызванных большим результатом запроса.  Можно изменить запрос, чтобы уменьшить число возвращаемых строк. Некоторые средства создания запросов позволяют добавлять в каждый запрос синтаксис "Top N".  Можно также CETAS результат запроса во временную таблицу, а затем использовать экспорт Polybase для обработки прежних версий.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца
При определении DDL с использованием наименьшего типа данных, который будет поддерживать ваши данные, повышает производительность запросов.  Это особенно важно для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Table overview], [типам данных таблиц][Table data types] и инструкции [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных
При временном размещении данных может оказаться, что использование таблицы кучи сделает общий процесс быстрее.  Загрузка таблицы в таблицу без кластеризованных индексов перед выполнением преобразования данных выполняется намного быстрее, чем загрузка данных в таблицу с кластеризованными индексами Columnstore.  

Кроме того, загрузка данных во временную таблицу выполняется гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с "#" и доступны только создавшему его сеансу, поэтому они могут работать только в ограниченных сценариях.   Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Дополнительные сведения см. в статьях о [временных таблицах][Temporary tables], инструкциях [CREATE TABLE][CREATE TABLE] и [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore
Кластеризованные индексы columnstore — это один из наиболее эффективных способов хранения данных в SQL Analytics.  По умолчанию таблицы в SQL Analytics создаются как кластеризованный индекс ColumnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  

Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  Качество сегмента можно изменить по числу строк в сжатой группе строк.  Дополнительные сведения об определении и улучшении качества сегмента для таблиц с кластеризованными индексами columnstore см. в разделе [Причины низкого качества индекса Columnstore][Causes of poor columnstore index quality] статьи [Индексирование таблиц в хранилище данных SQL][Table indexes].  

Так как высококачественные сегменты columnstore важны, рекомендуется использовать идентификаторы пользователей, которые находятся в классе ресурсов среднего или большого размера для загрузки данных. Использование [единиц использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Поскольку таблицы columnstore обычно не отправляют данные в сжатый сегмент columnstore до тех пор, пока не будет более 1 000 000 строк на таблицу и каждая таблица SQL Analytics будет секционирована по 60 таблицам, в качестве правила Thumb таблицы columnstore не смогут получить запрос, если только Таблица содержит более 60 000 000 строк.  Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Для таблицы с количеством строк менее 60 миллионов бессмысленно использовать индекс Columnstore, хотя он и не повлияет на производительность.  

Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица состоит из 100 секций, чтобы использовать кластеризованный индекс Columnstore, она должна состоять из как минимум 6 миллиардов строк (60 распределений * 100 секций * 1 миллион строк).  

Если таблица не содержит такого количества строк, рекомендуется уменьшить количество секций или использовать таблицу без кластеризованных индексов.  Чтобы получить более высокую производительность, возможно, вместо кластеризованной таблицы стоит использовать таблицу без кластеризованных индексов, содержащую вторичные индексы.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Ознакомьтесь также со статьями [Индексирование таблиц в хранилище данных SQL][Table indexes], [Руководство по индексам columnstore][Columnstore indexes guide] и разделом с описанием [повторной сборки индексов columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов
Аналитика SQL использует группы ресурсов в качестве способа выделения памяти для запросов.  В этом случае всем пользователям назначается небольшой класс ресурсов, который предоставляет 100 МБ памяти для каждого распределения.  В хранилище данных SQL содержится 60 распределений, на каждое из которых выделяется как минимум 100 МБ памяти. Поэтому в целом для системы выделяется 6000 МБ памяти (около 6 ГБ).  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  На выполнение некоторых запросов, таких как запросы на обычное сканирование, память не требуется.  На стороне зеркального отображения использование ресурсов большего размера сокращает параллелизм, поэтому необходимо учитывать это воздействие перед перемещением всех пользователей в большой класс ресурсов.

См. также [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Использование класса ресурсов небольшого размера для увеличения параллелизма
Если вы узнали, что запросы пользователей кажутся длительными, это может быть вызвано тем, что ваши пользователи работают в больших классах ресурсов и потребляют много слотов выдачи, в результате чего другие запросы помещаются в очередь.  Чтобы посмотреть очередь выполнения пользовательских запросов, выполните команду `SELECT * FROM sys.dm_pdw_waits`, которая позволяет просмотреть возвращенные строки.

См. также [классы ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md), [sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Другие ресурсы:
Дополнительные сведения о распространенных проблемах и решениях см. в статье [Устранение неполадок хранилища данных SQL Azure][Troubleshooting].

Если вы не нашли то, что ищете в этой статье, воспользуйтесь командой "Поиск документов" в левой части этой страницы, чтобы найти все документы Azure синапсе.  [Форум Azure синапсе][Azure SQL Data Warehouse MSDN Forum] — это место для задаваемых вопросов другим пользователям и группе продуктов Azure синапсе. 

Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Если вы предпочитаете задавать вопросы на Stack Overflow, у нас также есть [форум по Azure синапсе Stack overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Наконец, используйте страницу [обратной связи Azure синапсе][Azure SQL Data Warehouse Feedback] для выполнения запросов на функции.  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
