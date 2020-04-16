---
title: Рекомендации по пулам S'L
description: Рекомендации и рекомендации, которые вы должны знать, работая с пулами S'L.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427800"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Рекомендации по пулам S'L в аналитике Azure Synapse

В этой статье представлена коллекция рекомендаций, которые помогут вам достичь оптимальной производительности для пулов S'L в Azure Synapse Analytics. Ниже вы найдете основные рекомендации и важные области, чтобы сосредоточиться на, как вы строите свое решение. Каждый раздел знакомит вас с концепцией, а затем указывает вам на более подробные статьи, которые охватывают концепцию более подробно.

## <a name="sql-pools-loading"></a>Загрузка бассейнов S'L

Для руководства по загрузке бассейнов S'L [см.](data-loading-best-practices.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов

Для получения дополнительной информации о снижении затрат [Manage compute](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)за счет приостановки и масштабирования см.

## <a name="maintain-statistics"></a>Обеспечение статистики

В то время как S'L Server автоматически обнаруживает и создает или обновляет статистику по столбцам, пулы S'L требуют ручного обслуживания статистических данных. Для обеспечения оптимизации планов пула S'L необходимо вести статистику.  Планы, созданные с помощью оптимизатора, зависят от доступной статистики.

> [!TIP]
> Проще всего приступить к работе со статистикой, создав статистику для каждого столбца.  

Не менее важно также обновлять статистику, так как данные могут быть существенно изменены.  Рекомендуется выполнять обновление статистики ежедневно или после каждой загрузки.  Однако всегда есть компромиссы между производительностью и затратами на создание и обновление статистики.

Чтобы сократить время обслуживания статистики, будьте избирательны в отношении того, какие столбцы имеют статистику или нуждаются в наиболее частом обновлении. Например, можно обновить столбцы дат, где ежедневно могут добавляться новые значения. Сосредоточьтесь на том, чтобы статистика для столбцов, участвующих в соединениях, столбцах, используемых в положении WHERE, и столбцах, найденных в GROUP BY.

Дополнительную информацию о статистике можно найти в [таблицах управления статистикой,](develop-tables-statistics.md) [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)и [UPDATE STATISTICS.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы

Одноразовая загрузка на небольшой стол `INSERT INTO MyLookup VALUES (1, 'Type 1')`с заявлением INSERT, например, может быть лучшим подходом в зависимости от ваших потребностей. Однако, если вам нужно загрузить тысячи или миллионы строк в течение дня, вполне вероятно, что синглтон INSERTS не являются оптимальными.

Одним из способов решения этой проблемы является разработка одного процесса, который записывается в файл, а затем другой процесс периодической загрузки этого файла. Для получения дополнительной информации обратитесь к статье [INSERT.](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase

Пул спомощьи в пуле поддержки загрузки и экспорта данных с помощью нескольких инструментов, включая Azure Data Factory, PolyBase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  

> [!NOTE]
> Polybase является лучшим выбором при загрузке или экспорте больших объемов данных, или вам требуется более быстрая производительность.

Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO. CTAS сведет к минимуму регистрацию транзакций и является самым быстрым способом загрузки данных. Azure Data Factory также поддерживает нагрузки PolyBase и может достичь производительности, аналогичной CTAS. PolyBase поддерживает различные форматы файлов, включая файлы Gzip.

Чтобы максимизировать пропускную стоимость при использовании текстовых файлов Gzip, разбейте файлы на 60 или более файлов, чтобы максимизировать параллелизм вашей нагрузки. Кроме того, для повышения общей пропускной способности можно загружать данные одновременно. Дополнительная информация по темам, относящимся к данному разделу, включена в следующие статьи:

- [Загрузка данных](data-loading-overview.md)
- [руководством по использованию PolyBase](data-loading-best-practices.md)
- [Шаблоны загрузки и стратегии загрузки бассейнов ВС-L](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Загрузка данных с помощью фабрики данных Azure](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Перенос данных с помощью фабрики данных Azure](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [СОЗДАНИЕ ВНЕШНЕГО ФОРМАТА ФАЙЛА](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Создание таблицы по выбору (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним

Polybase не является оптимальным для запросов. Таблицы Polybase для пулов S'L в настоящее время поддерживают только файлы Azure blob и хранилище Azure Data Lake. Эти файлы не имеют каких-либо вычислительных ресурсов, поддерживающих их. В результате пулы S'L не могут разгрузить эту работу и должны прочитать весь файл, загрузив его в tempdb, чтобы он мог читать данные.

Если у вас есть несколько запросов для запроса этих данных, лучше загрузить эти данные один раз и запросы использовать локальную таблицу. Дальнейшее руководство Polybase включено в [Руководство по использованию статьи PolyBase.](data-loading-best-practices.md)

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц

По умолчанию таблицы распределяются по методу циклического перебора.   Этот по умолчанию упрощает для пользователей начало создавать таблицы без необходимости решать, как распределять их таблицы. Круглые столы Робин может выполнять достаточно для некоторых рабочих нагрузок. Но в большинстве случаев столбец распределения обеспечивает лучшую производительность.  

Наиболее распространенным примером таблицы, распределенной столбцей, превосходяющей таблицу Round Robin, является присоединении двух больших таблиц фактов.  

Например, если у вас есть таблица заказов, распределенная order_id, а таблица транзакций также распределена order_id, при присоединении таблицы заказов к таблице транзакций order_id этот запрос становится сквозным запросом. Операции перемещения данных затем устраняются. Чем меньше в запросе действий, тем быстрее он выполняется. Скорость выполнения запроса также зависит от объема перемещаемых данных.

> [!NOTE]
> При загрузке распределенной таблицы входящие данные не должны отсортироваться по ключу распределения. Это замедлит ваши нагрузки.

Ссылки на статьи, приведенные ниже, дадут вам дополнительную информацию об улучшении производительности путем выбора столбца распределения. Кроме того, вы найдете информацию о том, как определить распределенную таблицу в пункте С вашим заявлением CREATE TABLE:

- [Обзор таблицы](develop-tables-overview.md)
- [Распределение таблицы](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Выбор распределения таблицы](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Недопущение избыточного секционирования

В то время как данные раздела могут быть эффективными для поддержания ваших данных путем переключения разделов или оптимизации сканирования с помощью исключения раздела, наличие слишком большого количества разделов может замедлить ваши запросы.  Часто стратегия раздела с высокой гранулированностью, которая может хорошо работать на сервере S'L, может не хорошо работать в пуле S'L.  

Наличие слишком большого количества разделов может снизить эффективность кластерных индексов столбцов, если каждый раздел имеет менее 1 миллиона строк. Системы сезл автоматически делят данные на 60 баз данных. Таким образом, если создать таблицу со 100 перегородками, результат составит 6000 разделов. Каждая рабочая нагрузка отличается, поэтому лучший совет заключается в том, чтобы экспериментировать с разделительной, чтобы увидеть, что лучше всего работает для вашей рабочей нагрузки.  

Одним из вариантов рассмотрения является использование детализации, которая ниже, чем то, что вы реализовали с помощью сервера S'L. Например, следует использовать еженедельные или ежемесячные разделы вместо ежедневных разделов.

Более подробная информация о разделении подробно описана в статье [раздела таблицы.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций

ВЫПИСКА, ОБНОВЛЕНИЕ и DELETE операторы, запускаемые в транзакции. Когда они терпят неудачу, они должны быть откатили. Чтобы уменьшить вероятность длительного отката, минимизировать размеры транзакций, когда это возможно.  Минимизация размеров транзакций может быть выполнена путем деления инструкций INSERT, UPDATE и DELETE на части. Например, если у вас есть INSERT, который вы ожидаете занять 1 час, вы можете разбить INSERT на четыре части. Затем каждый запуск будет сокращен до 15 минут.  

> [!TIP]
> Использовать специальные случаи минимального регистра, такие как CTAS, TRUNCATE, DROP TABLE или INSERT, к пустым таблицам, чтобы снизить риск отката.  

Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо того, чтобы выполнять заявление DELETE для удаления всех строк в таблице, где order_date был в октябре 2001 года, можно ежемесячно разменять данные. Затем можно выключить раздел с данными для пустой раздела из другой таблицы (см. примеры ALTER TABLE).  

Для неразделенных таблиц рассмотрите возможность использования CTAS для записи данных, которые вы хотите сохранить в таблице, а не с помощью DELETE.  Если CTAS занимает столько же времени, это гораздо безопаснее для запуска, поскольку он имеет минимальный журнал транзакций и может быть отменен быстро, если это необходимо.

Дополнительная информация о содержании, отнесенном к данному разделу, содержится в статьях ниже:

- [Создание таблицы по выбору (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Основные сведения о транзакциях](develop-transactions.md)
- [Оптимизация транзакций](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Секционирование таблиц в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [ТРУНКАТЕ ТАБИ](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Уменьшение размеров результатов запроса

Уменьшение размеров результатов запроса позволяет избежать проблем со стороны клиента, вызванных большими результатами запросов.  Можно отсеять запрос, чтобы уменьшить количество возвращенных строк. Некоторые инструменты генерации запросов позволяют добавлять синтаксис "Top N" к каждому запросу.  Вы также можете CETAS результат запроса к временной таблице, а затем использовать экспорт PolyBase для обработки низшия.

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца

При определении DDL используйте наименьший тип данных, который будет поддерживать ваши данные, так как это улучшит производительность запроса.  Эта рекомендация особенно важна для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, определите столбцы как VARCHAR, когда это все, что необходимо, а не с помощью NVARCHAR.

Пожалуйста, ознакомьтесь с [обзором таблицы,](develop-tables-overview.md) [типами данных таблицы](develop-tables-data-types.md)и статьями [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для более детального анализа основных концепций, относящихся к вышеуказанной информации.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных

При временной посадке данных в пулах S'L таблицы кучи, как правило, удалят общий процесс быстрее.  Если данные загружаются только для того, чтобы поставить их перед запуском большего числа преобразований, загрузка таблицы в таблицу кучи будет быстрее, чем загрузка данных в кластерную таблицу столбцов.  

Загрузка данных в таблицу временных токов также будет загружаться гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с "К" и доступны только по созданной сессии. Следовательно, они могут работать только в ограниченных сценариях. Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Для получения дополнительных рекомендаций обратитесь к [временным таблицам,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)и CREATE TABLE [AS SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore

Кластерные индексы столбцов являются одним из наиболее эффективных способов хранения данных в пуле S'L.  По умолчанию таблицы в пуле S'L создаются как кластерный columnStore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  

Качество сегмента можно измерить по количеству строк в сжатой строке группы. О [причинах плохого качества индекса columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) в статье [«Индексы таблицы»](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) можно ознакомиться с инструкциями по обнаружению и улучшению качества сегмента для кластерных таблиц столбцов.  

Поскольку высококачественные сегменты магазинов столбцов важны, рекомендуется использовать идентифицировать пользователей, которые относятся к среднему или крупному классу ресурсов, для загрузки данных. Использование [единиц использования хранилища данных](resource-consumption-models.md) меньшего размера требует присвоения пользователю загрузки класса ресурсов большего размера.

Таблицы магазинов столбцов обычно не будут перемещать данные в сегмент сжатого столбцов до тех пор, пока на стол не будет более 1 миллиона строк. Каждый бильярдный стол S'L разделен на 60 таблиц. Таким образом, таблицы столбцов не приносят пользу запросу, если в таблице не имеется более 60 миллионов строк.  

> [!TIP]
> Для таблиц с менее чем 60 миллионами строк, наличие индекса столбцов не может быть оптимальным решением.  

При разделении данных каждый раздел должен иметь 1 миллион строк, чтобы воспользоваться кластерным индексом столбцов.  Для таблицы со 100 разделами она должна иметь не менее 6 миллиардов строк, чтобы воспользоваться кластерным хранилищем столбцов (60 дистрибутивов *100 разделов* 1 миллион строк).  

Если в таблице нет 6 миллиардов строк, у вас есть два основных варианта. Либо уменьшить количество разделов или рассмотреть возможность использования таблицы кучи вместо.  Также может быть стоит поэкспериментировать, чтобы увидеть, можно ли повысить производительность с помощью таблицы кучи со вторичными индексами, а не таблицой столбцов.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  Более подробную информацию о таблицах и столбцах индексов и можно найти в [таблице индексов](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore индексы руководства](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), и [Восстановление columnstore индексов](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) статей.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов

Пулы S'L используют группы ресурсов в качестве способа распределения памяти для запросов. Первоначально все пользователи присваиваются небольшому классу ресурсов, который предоставляет 100 МБ памяти на один дистрибутив.  Всегда 60 дистрибутивов. Каждому распределению дается не менее 100 МБ. Общее распределение общесистемной памяти составляет 6000 МБ, или чуть менее 6 ГБ.  

На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  Некоторые запросы, такие как чистое сканирование, не будут видеть никакой пользы. Использование больших классов ресурсов влияет на параллелизм. Таким образом, вы хотите, чтобы эти факты в виду, прежде чем перейти всех ваших пользователей в большой класс ресурсов.

Для получения дополнительной информации о классах ресурсов обратитесь к классам ресурсов для статьи [управления рабочей нагрузкой.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Используйте меньший класс ресурсов для увеличения параллелизма

Если вы заметили длительную задержку запросов пользователей, пользователи могут работать в больших классах ресурсов. Этот сценарий способствует потреблению слотов параллелизма, что может привести к тому, что другие запросы будут стоять в очереди.  Чтобы определить, стоят ли запросы `SELECT * FROM sys.dm_pdw_waits` пользователей в очереди, запустите, чтобы увидеть, если какие-либо строки были возвращены.

[Классы ресурсов для управления рабочей нагрузкой](../sql-data-warehouse/resource-classes-for-workload-management.md) и [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) статьи предоставят вам дополнительную информацию.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов

Пулы S'L имеют несколько DMV, которые могут быть использованы для мониторинга выполнения запросов.  В статье мониторинга ниже вы можете ознакомиться с пошаговой инструкцией о том, как просматривать детали выполнения запроса.  Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL. Для получения дополнительной подробной информации, пожалуйста, смотрите статьи, включенные в список ниже:

- [Мониторинг рабочей нагрузки с помощью динамических административных представлений](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Метки](develop-label.md)
- [Параметр](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Дальнейшие действия

Также смотрите статью [Troubleshooting](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) для общих вопросов и решений.

Если вам нужна информация, не представленная в этой статье, используйте "Поиск документов" на левой стороне этой страницы для поиска всех документов пула S'L.  [Форум бассейнов S'L](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) – это место, где вы можете задать вопросы другим пользователям и группе продуктов для пула.  

Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Если вы предпочитаете задавать свои вопросы по поводу переполнения стеков, у нас также есть [форум перелива стек Azure S'L.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Для запросов функций используйте страницу [обратной связи пула Azure S'L.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Добавление ваших запросов или других запросов на повторное голосование помогает нам сосредоточиться на наиболее востребованных функциях.
