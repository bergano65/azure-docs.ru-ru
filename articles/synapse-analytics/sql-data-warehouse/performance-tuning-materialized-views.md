---
title: Performance tuning with materialized views (Настройка производительности с помощью материализованных представлений)
description: Рекомендации и соображения, которые следует знать при использовании материализованных представлений для повышения производительности запроса.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743174"
---
# <a name="performance-tuning-with-materialized-views"></a>Performance tuning with materialized views (Настройка производительности с помощью материализованных представлений)

Материализованные представления в пуле Synapse S'L обеспечивают низкий метод обслуживания сложных аналитических запросов для быстрой производительности без каких-либо изменений запроса. В этой статье рассматриваются общие рекомендации по использованию материализованных представлений.

Материализованные представления в Хранилище данных Azure S'L обеспечивают низкий метод обслуживания сложных аналитических запросов для быстрой производительности без каких-либо изменений запроса. В этой статье рассматриваются общие рекомендации по использованию материализованных представлений.

## <a name="materialized-views-vs-standard-views"></a>Материализованные представления по сравнению со стандартными представлениями

Бассейн S'L поддерживает стандартные и материализованные представления.  Обе таблицы представляются с помощью выражений SELECT и представлены запросам в качестве логических таблиц.  Представления инкапсулируют сложность общих вычислений данных и добавляют слой абстракции в вычисления, поэтому нет необходимости переписывать запросы.  

Стандартное представление вычисляет свои данные каждый раз при использовании представления.  На диске нет данных, хранящихся. Обычно пользователи используют стандартные представления в качестве инструмента, помогая организовать логические объекты и запросы в базе данных.  Для использования стандартного представления запрос должен прямо ссылаться на него.

Материализованное представление предварительно вычисляет, хранит и поддерживает свои данные в пуле S'L точно так же, как таблица.  При использовании материализованного представления перерасчет не требуется.  Вот почему запросы, которые используют все или подмножество данных в материализованных представлениях, могут получить более высокую производительность.  Более того, запросы могут использовать материализованное представление без прямой ссылки на него, поэтому нет необходимости менять код приложения.  

Большинство требований, предъявляемых к стандартному представлению, по-прежнему применяются к материализованному представлению. Для получения подробной информации о материализованном синтаксисе представления и других требованиях, обратитесь к [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Сравнение                     | Просмотр                                         | Материализованное представление
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Просмотр определений                 | Хранится в пуле S'L.              | Хранится в пуле S'L.
|Просмотр содержимого                    | Генерируется каждый раз при использовании представления.   | Предварительно обработаны и хранятся в пуле S'L во время создания представления. Обновляется по мере добавления данных в базовые таблицы.
|Обновление данных                    | Всегда обновляется                               | Всегда обновляется
|Скорость получения данных представления из сложных запросов     | Медленно                                         | Быстрый  
|Дополнительное хранилище                   | нет                                           | Да
|Синтаксис                          | CREATE VIEW                                  | СОЗДАНИЕ МАТЕРИАЛИЗОВАННОГО ПРЕДСТАВЛЕНИЯ ПО ВЫБОРУ

## <a name="benefits-of-using-materialized-views"></a>Преимущества использования материализованных представлений

Правильно разработанное материализованное представление может обеспечить следующие преимущества:

- Сократите время выполнения сложных запросов с JOIN и агрегированными функциями. Чем сложнее запрос, тем выше потенциал экономии времени выполнения. Наибольшая выгода выжимается при высокой стоимости вычислений запроса и небольшом наборе данных.  
- Оптимизатор в пуле S'L может автоматически использовать развернутые материализованные представления для улучшения планов выполнения запросов.  Этот процесс является прозрачным для пользователей, обеспечивающих более быструю производительность запроса, и не требует, чтобы запросы направлялись к материализованным представлениям.
- Требуется низкое техническое обслуживание представлений.  Все постепенные изменения данных из базовых таблиц автоматически добавляются к материализованным представлениям синхронно.  Эта конструкция позволяет запрашивать материализованные представления для возврата тех же данных, что и непосредственное запрос о базовых таблицах.
- Данные в материализованном представлении могут быть распределены по-разному от базовых таблиц.  
- Данные в материализованных представлениях получают те же преимущества высокой доступности и устойчивости, что и данные в обычных таблицах.  

Материализованные представления, реализованные в пуле S'L, также предоставляют следующие дополнительные преимущества:

По сравнению с другими поставщиками хранилища данных материализованные представления, реализованные в Хранилище данных Azure S'L, также предоставляют следующие дополнительные преимущества:

- Автоматическое и синхронное обновление данных с изменениями данных в базовых таблицах. Вмешательство пользователя не требуется.
- Широкая агрегированная поддержка функций. Смотрите [CREATE MATERIALIZED VIEW AS SELECT (Трансакт-СЗЛ)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Поддержка рекомендации по конкретному запросу материализованного представления.  Смотрите [EXPLAIN (Трансакт-СЗЛ)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Распространенные сценарии  

Материализованные представления обычно используются в следующих сценариях:

**Необходимость повышения эффективности сложных аналитических запросов по размеру больших объемов**

Сложные аналитические запросы обычно используют больше функций агрегирования и соединения таблиц, вызывая больше вычислений-тяжелых операций, таких как перетасовки и соединения в исполнении запросов.  Вот почему эти запросы занимают больше времени, особенно на больших столах.  

Пользователи могут создавать материализованные представления для данных, возвращенных из общих вычислений запросов, поэтому нет необходимости перерасчета, когда эти данные необходимы для запросов, что позволяет снизить затраты на вычисления и ускорить ответ запроса.

**Нужна более быстрая производительность без изменений или минимальных запросов**

Изменения в схемах и запросах в пулах S'L, как правило, сведены к минимуму для поддержки регулярных операций ETL и отчетности.  Пользователи могут использовать материализованные представления для настройки производительности запроса, если затраты, понесенные представлениями, могут быть компенсированы увеличением производительности запроса.

По сравнению с другими вариантами настройки, такими как масштабирование и управление статистикой, создание и поддержание материализованного представления также выше.

- Создание или поддержание материализованных представлений не влияет на запросы, запущенные в отношении базовых таблиц.
- Оптимизатор запроса может автоматически использовать развернутые материализованные представления без прямой ссылки на представление в запросе. Эта возможность уменьшает необходимость изменения запроса в настройке производительности.

**Нужна другая стратегия распределения данных для более высокой производительности запроса**

Пул S'L — это распределенная массивно параллельная система обработки (MPP).   Данные в таблице пула S'L распределены по 60 узлам с использованием одной из трех [стратегий распределения](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (хэш, round_robin или реплицируется).  

Распределение данных указывается во время создания таблицы и остается неизменным до тех пор, пока таблица не будет удалена. Материализованное представление, являясь виртуальной таблицей на диске, поддерживает хэш и round_robin распределение данных.  Пользователи могут выбрать распределение данных, которое отличается от базовых таблиц, но оптимально для производительности запросов, которые используют представления больше всего.  

## <a name="design-guidance"></a>Руководство по проектированию

Вот общее руководство по использованию материализованных представлений для повышения производительности запроса:

**Дизайн для рабочей нагрузки**

Прежде чем приступить к созданию материализованных представлений, важно иметь глубокое понимание рабочей нагрузки с точки зрения шаблонов запросов, важности, частоты и размера полученных данных.  

Пользователи могут запускать EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> для материализованных представлений, рекомендованных оптимизатором запросов.  Поскольку эти рекомендации специфичны для запросов, материализованное представление, которое приносит пользу одному запросу, может оказаться неоптимальным для других запросов в той же рабочей нагрузке.  

Оцените эти рекомендации с учетом потребностей в рабочей нагрузке.  Идеальные материализованные представления являются теми, которые приносят пользу производительности рабочей нагрузки.  

**Будьте в курсе компромисса между более быстрыми запросами и стоимостью**

Для каждого материализованного представления есть стоимость хранения данных и затраты на поддержание представления.  По мере изменения данных в базовых таблицах увеличивается размер материализованного представления и изменяется его физическая структура.  Во избежание ухудшения производительности запроса каждое материализованное представление поддерживается отдельно движком пула S'L.  

Рабочая нагрузка на техническое обслуживание увеличивается при увеличении числа материализованных представлений и изменений базовой таблицы.   Пользователи должны проверить, могут ли затраты, понесенные из всех материализованных представлений, быть компенсированы повышением производительности запроса.  

Вы можете запустить этот запрос для списка материализованного представления в базе данных:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Варианты сокращения числа материализованных представлений:

- Определите общие наборы данных, часто используемые сложными запросами в рабочей нагрузке.  Создавайте материализованные представления для хранения этих наборов данных, чтобы оптимизатор мог использовать их в качестве строительных блоков при создании планов выполнения.  

- Отбросьте материализованные представления, которые имеют низкое использование или больше не нужны.  Отключенное материализованное представление не поддерживается, но оно по-прежнему несет расходы на хранение.  

- Объедините материализованные представления, созданные на тех же или аналогичных базовых таблицах, даже если их данные не пересекаются.  Расчесывание материализованных представлений может привести к большему размеру, чем сумма отдельных представлений, однако расходы на обслуживание представления должны сократиться.  Пример:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Не вся настройка производительности требует изменения запроса**

Оптимизатор пула S'L может автоматически использовать развернутые материализованные представления для повышения производительности запроса.  Эта поддержка применяется прозрачно к запросам, которые не ссылаются на представления и запросы, которые используют агрегаты, не поддерживаемые в создании материализованных представлений.  Изменение запроса не требуется. Можно проверить план расчетного выполнения запроса, чтобы подтвердить, используется ли материализованное представление.  

**Мониторинг материализованных представлений**

Материализованное представление хранится в пуле S'L точно так же, как таблица с кластерным индексом columnstore (CCI).  Чтение данных из материализованного представления включает сканирование сегментов индексов CCI и применение любых дополнительных изменений из базовых таблиц. Когда количество дополнительных изменений слишком высоко, разрешение запроса из материализованного представления может занять больше времени, чем непосредственное запрос базовых таблиц.  

Чтобы избежать ухудшения производительности запроса, хорошо заставить PDW_SHOWMATERIALIZEDVIEWOVERHEAD [DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) контролировать overhead_ratio представления (total_rows/max(1, base_view_row)).  Пользователи должны REBUILD материализованного представления, если его overhead_ratio слишком высока.

**Материализованное представление и кэширование набора результатов**

Эти две функции вводятся в пул СЗЛ примерно в то же время для настройки производительности запроса.  Кэширование набора результатов используется для получения высокой параллелизма и быстрого отклика от повторяющихся запросов на статические данные.  

Для использования кэшированного результата форма запроса запроса кэша должна совпадать с запросом, который произвел кэш.  Кроме того, кэшированный результат должен применяться ко всему запросу.  

Материализованные представления позволяют изменять данные в базовых таблицах.  Данные в материализованных представлениях могут быть применены к фрагменту запроса.  Эта поддержка позволяет использовать одни и те же материализованные представления различными запросами, которые разделяют некоторые вычисления для более высокой производительности.

## <a name="example"></a>Пример

В этом примере используется запрос, похожий на TPCDS, который находит клиентов, которые тратят больше денег через каталог, чем в магазинах, идентифицируют предпочтительных клиентов и страну их происхождения.   Запрос включает в себя выбор записей TOP 100 из UNION трех суб-SELECT заявлений с участием SUM() и GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Проверьте план сметного выполнения запроса.  Есть 18 перетасовок и 17 присоединяется операций, которые занимают больше времени для выполнения. Теперь давайте создадим одно материализованное представление для каждой из трех суб-SELECT инструкций.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Проверьте план выполнения исходного запроса еще раз.  Теперь количество соединений меняется с 17 до 5, и перетасовки больше нет.  Нажмите значок операции фильтра в плане, его список вывода показывает, что данные считываются из материализованных представлений вместо базовых таблиц.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

С материализованными представлениями один и тот же запрос выполняется гораздо быстрее без каких-либо изменений кода.  

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке можно ознакомьтесь с [обзором разработки пула Synapse S'L.](sql-data-warehouse-overview-develop.md)
