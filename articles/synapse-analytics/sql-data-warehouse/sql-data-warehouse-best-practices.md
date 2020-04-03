---
title: Рекомендации по аналитике S'L в azure Synapse Analytics (ранее S'L DW)
description: Рекомендации и рекомендации по разработке решений для аналитики S'L в Azure Synapse Analytics (ранее S'L DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 44dbc03a41cfde94c344ae331b21d7536778050c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619098"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Рекомендации по аналитике S'L в azure Synapse Analytics (ранее S'L DW)

Данная статья представляет собой набор рекомендаций, которые помогут вам достичь оптимальной производительности в результате развертывания [S'L Analytics.](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)  Цель этой статьи состоит в том, чтобы дать вам некоторые основные рекомендации и выделить важные области внимания.  Каждый раздел знакомит вас с концепцией, а затем указывает вам на более подробные статьи, которые охватывают концепцию более подробно. Последовательность тем находится в порядке важности. 

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Дополнительные сведения о сокращении затрат при помощи приостановки и масштабирования см. в статье об [управлении вычислительными ресурсами](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Обеспечение статистики
Хранилище данных Azure S'L может быть настроено для автоматического обнаружения и создания статистики по столбцам.  Планы запросов, созданные оптимизатором, хороши только так же хорошо, как и доступная статистика.  Мы рекомендуем включить AUTO_CREATE_STATISTICS для баз данных и обновлять статистику ежедневно или после каждой загрузки, чтобы гарантировать, что статистика столбцов, используемых в ваших запросах, всегда актуальна. 

Если вы обнаружите, что обновление всех статистических данных занимает слишком много времени, вы можете попытаться быть более избирательными в отношении того, какие столбцы нуждаются в частых обновлениях статистики. Например, можно ежедневно обновлять столбцы дат, в которые добавляются новые значения. **Наибольшую выгоду вы получите, обновив статистику по столбикам, участвующим в соединениях, столбцам, используемым в оговорке WHERE, и столбцам, найденным в GROUP BY.**

Дополнительные сведения см. в статьях [Управление статистикой таблиц в хранилище данных SQL][Manage table statistics], [CREATE STATISTICS (Transact-SQL)][CREATE STATISTICS] и [UPDATE STATISTICS (Transact-SQL)][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов
В аналитической компании S'L Analytics есть несколько DMV, которые могут быть использованы для мониторинга выполнения запросов.  В первой статье в списке ниже предоставлены пошаговые инструкции по отслеживанию выполнения запроса.  Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL.

Дополнительные сведения см. в статьях [Мониторинг рабочей нагрузки с помощью динамических административных представлений][Monitor your workload using DMVs], [Использование меток для инструментирования запросов в хранилище данных SQL][LABEL], [Предложение OPTION (Transact-SQL)][OPTION], [sys.dm_exec_sessions (Transact-SQL)][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests (Transact-SQL)][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_request_steps (Transact-SQL)][sys.dm_pdw_sql_requests], [ys.dm_pdw_dms_workers (Transact-SQL)], [DBCC PDW_SHOWEXECUTIONPLAN (Transact-SQL)][DBCC PDW_SHOWEXECUTIONPLAN] и [sys.dm_pdw_waits (Transact-SQL)][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Настройка производительности запроса с помощью новых усовершенствований продукта
- [Performance tuning with materialized views](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views) (Настройка производительности с помощью материализованных представлений)
- [Performance tuning with result set caching](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci) (Настройка производительности с помощью упорядоченного кластеризованного индекса columnstore)
- [Performance tuning with result set caching](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching) (Настройка производительности путем кэширования результирующего набора)

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы
Одноразовую загрузку в небольшую таблицу или даже периодическую перезагрузку результатов поиска рекомендуется выполнять, используя такой синтаксис инструкции INSERT: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Однако для загрузки тысяч или миллионов строк на протяжении дня одноэлементных инструкций INSERT может быть недостаточно.  Вместо них можно создать процессы, которые будут записывать инструкции INSERT в файл и периодически загружать его.

Дополнительные сведения см. в статье [Инструкция INSERT (Transact-SQL)][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

 Аналитика s'L поддерживает загрузку и экспорт данных с помощью нескольких инструментов, включая Azure Data Factory, PolyBase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  Однако для загрузки или экспорта данных большого объема, что требует высокой производительности, лучше всего использовать PolyBase.  
 
 PolyBase предназначен для использования архитектуры MPP (Massively Parallel Processing) и будет загружать и экспортировать величину данных быстрее, чем любой другой инструмент.  Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO.  **Команда CTAS позволяет свести к минимуму нагрузку ведения журнала транзакций и быстрее всего выполнить загрузку данных.** 
 
  Фабрика данных Azure также поддерживает нагрузки PolyBase и способна достичь производительности, сопоставимой с CTAS.  PolyBase поддерживает различные форматы файлов, включая формат GZIP.  **Чтобы максимизировать пропускную стоимость при использовании текстовых файлов gzip, разбейте файлы на 60 или более файлов, чтобы максимизировать параллелизм вашей нагрузки.**  Кроме того, для повышения общей пропускной способности можно загружать данные одновременно.

Смотрите также [данные о загрузке,][Load data]Руководство по использованию [шаблонов и стратегий загрузки пула][Azure SQL Data Warehouse loading patterns and strategies] [PolyBase,][Guide for using PolyBase]S'L, [Загрузка данных с Azure Data Factory,][Load Data with Azure Data Factory] [Перемещение данных с Azure Data Factory][Move data with Azure Data Factory], CREATE EXTERNAL FILE [FORMAT][CREATE EXTERNAL FILE FORMAT], [Создание таблицы как выбор (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним
Технология Polybase (т. н. внешние таблицы), возможно, является самым быстрым способом загрузки данных, но она не оптимальна с точки зрения запросов. Таблицы Polybase в настоящее время поддерживают только файлы Azure blob и хранилище Azure Data Lake. Эти файлы не обслуживаются какими-либо вычислительными ресурсами.  

В результате, s'L Analytics не может разгрузить эту работу и, следовательно, должен прочитать весь файл, загрузив его в tempdb для того, чтобы прочитать данные.  Таким образом, если будет отправлено несколько запросов к этим данным, то лучше один раз загрузить их в локальную таблицу и предоставить ее для выполнения запросов.

Ознакомьтесь также с [руководством по использованию PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц
По умолчанию таблицы распределяются по методу циклического перебора.  Это позволяет упростить процесс создания таблиц, так как пользователям не нужно принимать решение о типе распределения.  Таблицы с распределением по методу циклического перебора могут вполне годиться для некоторых рабочих нагрузок, но в большинстве случаев намного эффективнее использовать столбец распределения.  Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  

Например, во время выполнения запроса на объединение таблицы Orders, распределенной по идентификатору order_id, с таблицей Transactions, распределенной по тому же идентификатору, этот запрос превращается в запрос к серверу, что исключает выполнение операций перемещения данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.  

При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  Более подробная информация о том, как выбор столбца распределения может повысить производительность, а также определить распределенную таблицу в пункте с оговоркой CREATE TABLE, смотрите ниже ссылки.

Дополнительные сведения см. в статьях [Общие сведения о таблицах в хранилище данных SQL][Table overview], [Распределение таблиц в хранилище данных SQL][Table distribution], а также в статьях, посвященных [выбору распределения таблицы][Selecting table distribution], [инструкции CREATE TABLE][CREATE TABLE] и [инструкции CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования
В то время как данные раздела могут быть эффективными для поддержания ваших данных путем переключения разделов или оптимизации сканирования с помощью исключения раздела, наличие слишком большого количества разделов может замедлить ваши запросы.  Часто стратегия раздела с высокой гранулированностью, которая может хорошо работать на сервере S'L, может не хорошо работать в S'L Analytics.  

Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк.  Имейте в виду, что за кулисами, S'L Analytics перерезает ваши данные для вас в 60 баз данных, так что если вы создаете таблицу с 100 разделов, это на самом деле приводит к 6000 разделов.  

Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  В хранилище данных SQL можно использовать меньшее количество секций, чем в SQL Server.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций
Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  

Например, если у вас есть INSERT, который, по возможности, займет 1 час, разбейте INSERT на четыре части, каждая из которых будет проходить за 15 минут.  К пустым таблицам можно применять специальные операции, которые сопровождаются записью в журнал минимальных сведений, (такие как CTAS, TRUNCATE, DROP TABLE или INSERT), чтобы снизить риск отката.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, упорядоченной по идентификатору order_date (октябрь 2001 г.), данные можно секционировать ежемесячно, а потом переключить секцию с данными на пустую секцию из другой таблицы (см. примеры использования инструкции ALTER TABLE).  

Для неразделенных таблиц рассмотрите возможность использования CTAS для записи данных, которые вы хотите сохранить в таблице, а не с помощью DELETE.  Если CTAS занимает столько же времени, это гораздо безопаснее работать, поскольку она имеет минимальный журнал записи транзакций и может быть отменена быстро, если это необходимо.

Дополнительные сведения см. в статьях [Транзакции в хранилище данных SQL][Understanding transactions], [Оптимизация транзакций для хранилища данных SQL][Optimizing transactions], [Секционирование таблиц в хранилище данных SQL][Table partitioning], а также в статьях об инструкциях [TRUNCATE TABLE][TRUNCATE TABLE] и [ALTER TABLE][ALTER TABLE] и статье [Функция Create Table As Select (CTAS) в хранилище данных SQL][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Уменьшение размеров результатов запроса  
Этот шаг помогает избежать проблем со стороны клиента, вызванных большим результатом запроса.  Можно отсеять запрос, чтобы уменьшить количество возвращенных строк. Некоторые инструменты генерации запросов позволяют добавлять синтаксис "Top N" к каждому запросу.  Вы также можете CETAS результат запроса к временной таблице, а затем использовать экспорт PolyBase для обработки низшия.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца
При определении DDL использование наименьшего типа данных, который будет поддерживать ваши данные, улучшит производительность запроса.  Это особенно важно для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Table overview], [типам данных таблиц][Table data types] и инструкции [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных
При временной посадке данных, вы можете обнаружить, что с помощью таблицы кучи сделает общий процесс быстрее.  Если вы загружаете данные только для промежуточного хранения перед их последующими преобразованиями, загрузка таблицы в таблицу кучи будет происходить значительно быстрее по сравнению с загрузкой данных в кластеризованную таблицу columnstore.  

Кроме того, загрузка данных во временную таблицу выполняется гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с "К" и доступны только по созданной сессии, поэтому они могут работать только в ограниченных сценариях.   Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Дополнительные сведения см. в статьях о [временных таблицах][Temporary tables], [инструкциях CREATE TABLE][CREATE TABLE] и [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore
Кластерные индексы столбцов являются одним из наиболее эффективных способов хранения данных в S'L Analytics.  По умолчанию таблицы в S'L Analytics создаются как кластерный columnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  

Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  Качество сегмента можно изменить по числу строк в сжатой группе строк.  Дополнительные сведения об определении и улучшении качества сегмента для таблиц с кластеризованными индексами columnstore см. в разделе [Причины низкого качества индекса Columnstore][Causes of poor columnstore index quality] статьи [Индексирование таблиц в хранилище данных SQL][Table indexes].  

Поскольку высококачественные сегменты магазинов столбцов важны, рекомендуется использовать идентифицировать пользователей, которые относятся к среднему или крупному классу ресурсов, для загрузки данных. Использование [единиц использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Поскольку таблицы столбцов обычно не будут перемещать данные в сегмент сжатого столбцов до тех пор, пока на стол не будет более 1 миллиона строк, и каждая таблица S'L Analytics не будет разделена на 60 таблиц, как правило, таблицы столбцов не принесут пользу запросу, если только в таблице не будет более 60 миллионов строк.  Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Для таблицы с количеством строк менее 60 миллионов бессмысленно использовать индекс Columnstore, хотя он и не повлияет на производительность.  

Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица состоит из 100 секций, чтобы использовать кластеризованный индекс Columnstore, она должна состоять из как минимум 6 миллиардов строк (60 распределений * 100 секций * 1 миллион строк).  

Если таблица не содержит такого количества строк, рекомендуется уменьшить количество секций или использовать таблицу без кластеризованных индексов.  Чтобы получить более высокую производительность, возможно, вместо кластеризованной таблицы стоит использовать таблицу без кластеризованных индексов, содержащую вторичные индексы.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Ознакомьтесь также со статьями [Индексирование таблиц в хранилище данных SQL][Table indexes], [Руководство по индексам columnstore][Columnstore indexes guide] и разделом с описанием [повторной сборки индексов columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов
В качестве способа распределения памяти для запросов аналитика S'L Analytics использует группы ресурсов.  Из коробки, все пользователи назначаются к небольшому классу ресурсов, который предоставляет 100 МБ памяти в распределении.  В хранилище данных SQL содержится 60 распределений, на каждое из которых выделяется как минимум 100 МБ памяти. Поэтому в целом для системы выделяется 6000 МБ памяти (около 6 ГБ).  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  На выполнение некоторых запросов, таких как запросы на обычное сканирование, память не требуется.  С другой стороны, использование больших классов ресурсов снижает параллелизм, поэтому вы захотите принять это влияние во внимание, прежде чем переходить всех пользователей в большой класс ресурсов.

См. также [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Использование класса ресурсов небольшого размера для увеличения параллелизма
Если вы замечаете, что запросы пользователей, кажется, имеют длительную задержку, это может быть то, что ваши пользователи работают в больших классах ресурсов и потребляют много слотов для параллелизма, вызывая другие запросы в очереди.  Чтобы посмотреть очередь выполнения пользовательских запросов, выполните команду `SELECT * FROM sys.dm_pdw_waits`, которая позволяет просмотреть возвращенные строки.

См. также [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="other-resources"></a>Другие ресурсы
Дополнительные сведения о распространенных проблемах и решениях см. в статье [Устранение неполадок хранилища данных SQL Azure][Troubleshooting].

Если вы не нашли то, что искали в этой статье, попробуйте использовать «Поиск документов» в левой части этой страницы для поиска всех документов Azure Synapse.  [Форум Azure Synapse][Azure SQL Data Warehouse MSDN Forum] — это место, где вы можете задавать вопросы другим пользователям и группе продуктов Azure Synapse. 

Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Если вы предпочитаете задавать свои вопросы по переполнению стеков, у нас также есть [Форум перелива стек Azure Synapse.][Azure SQL Data Warehouse Stack Overflow Forum]

Наконец, пожалуйста, используйте страницу [отзывов Azure Synapse][Azure SQL Data Warehouse Feedback] для запросов функций.  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

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
