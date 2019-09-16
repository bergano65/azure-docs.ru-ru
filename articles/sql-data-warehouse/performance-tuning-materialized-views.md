---
title: Настройка производительности в материализованных представлениях хранилища данных SQL Azure | Документация Майкрософт
description: Рекомендации и соображения, которые следует знать при использовании материализованных представлений для повышения производительности запросов.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 85c2607ae163ab2d29a53440cd65672bdbe0fddf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985354"
---
# <a name="performance-tuning-with-materialized-views"></a>Настройка производительности с материализованными представлениями 
Материализованные представления в хранилище данных SQL Azure обеспечивают низкий способ обслуживания сложных аналитических запросов, чтобы обеспечить высокую производительность без изменения запроса. В этой статье рассматриваются общие рекомендации по использованию материализованных представлений.


## <a name="materialized-views-vs-standard-views"></a>Материализованные представления и стандартные представления
Хранилище данных SQL Azure поддерживает стандартные и материализованные представления.  Оба являются виртуальными таблицами, созданными с помощью выражений SELECT и представленными для запросов в виде логических таблиц.  Представления инкапсулируют сложность общих вычислений данных и добавляют уровень абстракции к вычислительным изменениям, поэтому нет необходимости переписывать запросы.  

Стандартное представление каждый раз рассчитывает данные при каждом использовании представления.  На диске не хранятся данные. Обычно пользователи используют стандартные представления в качестве средства, помогающего организовать логические объекты и запросы в базе данных.  Чтобы использовать стандартное представление, запрос должен создать прямую ссылку на него. 

Материализованные представления предварительно вычисляются, сохраняют и сохраняют свои данные в хранилище данных SQL Azure так же, как и таблица.  При использовании материализованных представлений повторное вычисление не требуется.  Именно поэтому запросы, использующие все или подмножество данных в материализованных представлениях, могут повысить производительность.  Более того, запросы могут использовать материализованные представления, не внося прямые ссылки на него, поэтому нет необходимости изменять код приложения.  

Большинство требований к стандартному представлению по-прежнему применимо к материализованным представлениям. Дополнительные сведения о синтаксисе материализованных представлений и других требованиях см. в статье [Создание материализованных ПРЕДСТАВЛЕНИЙ как SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).



| Сравнение                     | Вид                                         | Материализованное представление             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|Просмотреть определение                 | Хранятся в хранилище данных Azure.              | Хранятся в хранилище данных Azure.    
|Просмотр содержимого                    | Создается каждый раз при использовании представления.   | Предварительно обработано и сохранено в хранилище данных Azure во время создания представления. Обновляются по мере добавления данных в базовые таблицы.                                             
|Обновление данных                    | Всегда Обновлено                               | Всегда Обновлено                          
|Скорость получения данных представления из сложных запросов     | Производительность                                         | Быстро  
|Дополнительное хранилище                   | Нет                                           | Да                             
|Синтаксис                          | СОЗДАТЬ ПРЕДСТАВЛЕНИЕ                                  | СОЗДАТЬ МАТЕРИАЛИЗОВАННЫЙ ВИД КАК SELECT           
     
## <a name="benefits-of-using-materialized-views"></a>Преимущества использования материализованных представлений

Правильно спроектированное материализованные представление может предоставить следующие преимущества:

- Сократите время выполнения сложных запросов с помощью соединений и агрегатных функций. Чем сложнее запрос, тем выше вероятность сохранения во время выполнения. Наиболее выгодное преимущество заключается в том, что стоимость вычислений запроса высока и результирующий набор данных мал.  

- Оптимизатор в хранилище данных SQL Azure может автоматически использовать развернутые материализованные представления для улучшения планов выполнения запросов.  Этот процесс является прозрачным для пользователей, обеспечивающих более высокую производительность запросов и не требующий прямой ссылки на материализованные представления. 

- Требовать низкое обслуживание представлений.  Материализованный режим сохраняет данные в двух местах: кластеризованный индекс columnstore для начальных данных во время создания представления и разностное хранилище для добавочных изменений данных.  Все изменения данных из базовых таблиц автоматически добавляются в разностное хранилище синхронным образом.  Фоновый процесс (перемещение кортежей) периодически перемещает данные из разностного хранилища в индекс columnstore представления.  Такая схема позволяет выполнять запросы материализованных представлений для возврата тех же данных, что и непосредственное выполнение запросов к базовым таблицам. 
- Данные в материализованных представлениях можно распределять по-разному из базовых таблиц.  
- Данные в материализованных представлениях получают те же высокие преимущества и надежность, что и данные в обычных таблицах.  
 
По сравнению с другими поставщиками хранилища данных материализованные представления, реализованные в хранилище данных SQL Azure, также предоставляют следующие дополнительные преимущества: 

- Автоматическое и синхронное обновление данных с изменениями данных в базовых таблицах. Никаких действий от пользователя не требуется. 
- Широкая поддержка агрегатных функций. См. раздел [CREATE материализованный вид как SELECT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).
- Поддержка конкретной рекомендации по материализованным представлениям запросов.  См. раздел [объяснить (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Распространенные сценарии  

Материализованные представления обычно используются в следующих сценариях: 

**Необходимость повышения производительности сложных аналитических запросов к большим объемам данных**

Сложные аналитические запросы обычно используют больше агрегатных функций и соединений таблиц, что приводит к большим объемам вычислительных операций, таким как перезапись в случайный порядок и соединение при выполнении запроса.  Именно поэтому выполнение запросов занимает больше времени, чем в больших таблицах.  Пользователи могут создавать материализованные представления для данных, возвращаемых из общих вычислений запросов, поэтому нет необходимости выполнять перерасчеты, если эти данные необходимы для запросов, что позволяет снизить стоимость вычислений и ускорить отклики на запросы. 

**Требуется более высокая производительность при отсутствии или минимальных изменениях запроса**

Изменения схемы и запросов в хранилищах данных обычно хранятся как минимум для поддержки обычных операций ETL и создания отчетов.  Пользователи могут использовать материализованные представления для настройки производительности запросов, если затраты, понесенные представлениями, могут быть смещены по росту производительности запросов. В сравнении с другими параметрами настройки, такими как управление масштабированием и статистикой, это значительно менее интенсивное изменение рабочей среды для создания и обслуживания материализованных представлений, а также его потенциального выигрыша в производительности.

- Создание или обслуживание материализованных представлений не влияет на запросы, выполняемые с базовыми таблицами.
- Оптимизатор запросов может автоматически использовать развернутые материализованные представления без прямой ссылки на просмотр в запросе. Эта возможность снижает потребность в изменении запроса при настройке производительности. 

**Необходима другая стратегия распределения данных для повышения производительности запросов.**

Хранилище данных Azure — это распределенная система массовой параллельной обработки (MPP).   Данные в таблице хранилища данных распределяются между 60 узлами с помощью одной из трех [стратегий распределения](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute) (hash, round_robin или REPLICATE).  Распределение данных указывается в момент создания таблицы и остается неизменным до тех пор, пока таблица не будет удалена. Материализованные представления, являющиеся виртуальными таблицами на диске, поддерживают распределение данных hash и round_robin.  Пользователи могут выбрать распределение данных, отличное от базовых таблиц, но оптимальное для производительности запросов, использующих эти представления.  

## <a name="design-guidance"></a>Руководство по проектированию 

Ниже приведены общие рекомендации по использованию материализованных представлений для повышения производительности запросов.

**Разработка для рабочей нагрузки**

- Прежде чем приступать к созданию материализованных представлений, важно иметь глубокое понимание рабочей нагрузки с точки зрения шаблонов запросов, важности, частоты и размера результирующих данных.  

- Пользователи могут выполнять объяснения WITH_RECOMMENDATIONS < SQL_statement > для материализованных представлений, рекомендованных оптимизатором запросов.  Так как эти рекомендации относятся к конкретным запросам, материализованный вид, который выгодно для одного запроса, может быть неоптимальным для других запросов в той же рабочей нагрузке.  Оцените эти рекомендации с учетом потребностей рабочей нагрузки.  Идеальными материализованными представлениями являются преимущества, которые приводят к повышению производительности рабочей нагрузки.  

**Учитывайте компромисс между быстрыми запросами и стоимостью** 

- Для каждого материализованных представлений существуют затраты на хранение и стоимость обслуживания представления с помощью перемещения кортежей. На каждый экземпляр сервера хранилища данных SQL Azure входит один кортеж.  При наличии слишком большого количества материализованных представлений Рабочая нагрузка для перемещения кортежей увеличится, и производительность запросов, использующих материализованные представления, может снизиться, если перемещение кортежей не сможет быстро переместить данные в сегменты индекса.  Пользователи должны проверить, могут ли затраты, понесенные во все материализованные представления, быть смещены на рост производительности запроса.  Выполнить этот запрос для списка материализованных представлений в базе данных: 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Параметры для сокращения числа материализованных представлений: 

- Выявление общих наборов данных, часто используемых сложными запросами в рабочей нагрузке.  Создавайте материализованные представления для хранения этих наборов данных, чтобы оптимизатор мог использовать их в качестве стандартных блоков при создании планов выполнения.  

- Удалите материализованные представления, которые имеют низкий уровень использования или больше не нужны.  Отключенное материализованные представление не поддерживается, но по-прежнему приводит к затратам на хранение.  

- Объедините материализованные представления, созданные в тех же или аналогичных базовых таблицах, даже если их данные не пересекаются.  Объединение материализованных представлений может привести к увеличению размера представления, чем сумма отдельных представлений, однако стоимость обслуживания представления должна сократиться.  Пример:

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

**Не все настройки производительности требуют изменения запроса.**

Оптимизатор хранилища данных может автоматически использовать развернутые материализованные представления для повышения производительности запросов.  Эта поддержка прозрачно применяется к запросам, которые не ссылаются на представления и запросы, использующие агрегаты, не поддерживаемые при создании материализованных представлений.  Изменение запроса не требуется. Вы можете проверить предполагаемый план выполнения запроса, чтобы подтвердить использование материализованных представлений.  

**Мониторинг материализованных представлений** 

Материализованные представления хранятся в хранилище данных точно так же, как таблица с кластеризованным индексом columnstore (CCI).  Чтение данных из материализованных представлений включает сканирование индекса и применение изменений из разностного хранилища.  Если число строк в разностном хранилище слишком велико, разрешение запроса из материализованных представлений может занять больше времени, чем непосредственное выполнение запросов к базовым таблицам.  Чтобы избежать снижения производительности запросов, рекомендуется выполнить [инструкцию DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest) для мониторинга overhead_ratio представления (total_rows/base_view_row).  Если overhead_ratio слишком высокий, рассмотрите возможность перестроения материализованных представлений, чтобы все строки в разностном хранилище перемещались в индекс columnstore.  

**Материализованные представления и кэширование результирующего набора**

Эти две функции вводятся в хранилище данных SQL Azure примерно в одно и то же время для настройки производительности запросов.  Кэширование результирующего набора используется для получения высокого параллелизма и быстрого ответа от повторяющихся запросов к статическим данным.  Чтобы использовать кэшированный результат, форма запроса кэша должна совпадать с запросом, создавшим кэш.  Кроме того, кэшированный результат должен применяться ко всему запросу.  Материализованные представления позволяют изменять данные в базовых таблицах.  Данные в материализованных представлениях могут быть применены к части запроса.  Эта поддержка позволяет использовать одни и те же материализованные представления для разных запросов, которые используют некоторые вычисления для повышения производительности.

## <a name="example"></a>Пример

В этом примере используется запрос, подобный ТПКДС, который находит клиентов, занимающих больше денег по каталогу, чем в магазинах, и определяет предпочтительных клиентов и их страну происхождения.   Запрос включает выбор первых 100 записей из объединения трех вложенных инструкций SELECT, включающих SUM () и GROUP BY. 

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

Проверьте предполагаемый план выполнения запроса.  18 в случайном порядке и 17 операций присоединяется, что требует больше времени на выполнение. Теперь давайте создадим одно материализованные представление для каждой из трех инструкций подзапроса ВЫБОРки.   

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
Проверьте план выполнения исходного запроса еще раз.  Теперь количество присоединений изменяется с 17 до 5 и больше не существует.  Щелкните значок операции фильтрации в плане, его список выходных данных показывает, что данные считываются из материализованных представлений, а не из базовых таблиц.  

 ![Plan_Output_List_with_Materialized_Views](media/performance-tuning-materialized-views/output-list.png)

При использовании материализованных представлений один и тот же запрос выполняется гораздо быстрее без изменения кода.  

## <a name="next-steps"></a>Следующие шаги
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
