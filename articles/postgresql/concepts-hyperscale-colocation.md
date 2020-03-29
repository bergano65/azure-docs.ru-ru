---
title: Таблица colocation - Гипермасштаб (Citus) - База данных Azure для PostgreS'L
description: Как хранить связанную с ней информацию для более быстрых запросов
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967343"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Коклокация таблиц ы в базе данных Azure для PostgreS'L - Гипермасштаб (Citus)

Colocation означает хранение соответствующей информации вместе на одних и тех же узлах. Запросы могут идти быстро, когда все необходимые данные доступны без какого-либо сетевого трафика. Совместное размещение связанных данных на различных узлах позволяет запросам эффективно выполняться параллельно на каждом узлу.

## <a name="data-colocation-for-hash-distributed-tables"></a>Колокация данных для распределенных таблиц хэш-распределенных

В базе данных Azure для PostgreS'L - Hyperscale (Citus) строка хранится в осколок, если хэш значения в столбце распределения попадает в диапазон хэша shard. Осколки с одинаковым хэш-диапазоном всегда размещаются на одном узло. Строки с равными значениями столбца распределения всегда находятся на одном и том же узлах между таблицами.

![Осколки](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Практический пример колокации

Рассмотрим следующие таблицы, которые могут быть частью мульти-тенант веб-аналитики SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Теперь мы хотим ответить на запросы, которые могут быть выпущены панелью мониторинга, ориентированной на клиента. Примером запроса является "Вернуть количество посещений на прошлой неделе для всех страниц, начиная с '/блог' в арендаторе шесть".

Если бы наши данные были в варианте развертывания одного сервера, мы могли бы легко выразить наш запрос, используя богатый набор реляционных операций, предлагаемых S'L:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

До тех пор, пока [рабочий набор](https://en.wikipedia.org/wiki/Working_set) для этого запроса вписывается в память, таблица с одним сервером является подходящим решением. Рассмотрим возможности масштабирования модели данных с помощью опции развертывания Hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Распределить таблицы по идентификатору

Односерверные запросы начинают замедляться по мере роста числа арендаторов и данных, хранящихся для каждого арендатора. Рабочий набор перестает вписываться в память, а процессор становится узким местом.

В этом случае мы можем осколки данных во многих узлах с помощью Hyperscale (Citus). Первый и самый важный выбор, который мы должны сделать, когда мы решим осколок является колонка распределения. Начнем с наивного выбора `event_id` использования для таблицы событий и `page_id` для таблицы: `page`

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Когда данные рассредоточены по разным работникам, мы не можем выполнить соединение, как мы бы на одном узле PostgreS'L. Вместо этого нам нужно выпустить два запроса:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

После этого результаты этих двух шагов должны быть объединены приложением.

Для выполнения запросов необходимо консультироваться с данными в осколках, разбросанных по узлам.

![Неэффективные запросы](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

В этом случае распределение данных создает существенные недостатки:

-   Накладные расходы от запроса каждого осколка и запуска нескольких запросов.
-   Накладные расходы no 1 возвращают клиенту много строк.
-   No 2 становится большим.
-   Необходимость написания запросов в несколько этапов требует изменений в приложении.

Данные рассредоточены, поэтому запросы могут быть параллельны. Это только полезно, если объем работы, что запрос делает значительно больше, чем накладные расходы запроса многих осколков.

### <a name="distribute-tables-by-tenant"></a>Распределить таблицы по арендатору

В Hyperscale (Citus) строки с одинаковым значением столбца распределения гарантированно находятся на одном узло. Начиная сначала, мы можем `tenant_id` создавать наши таблицы с помощью столбца распределения.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Теперь Hyperscale (Citus) может ответить на оригинальный односерверный запрос без изменений (No1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Из-за фильтра и соединения на tenant_id, Hyperscale (Citus) знает, что весь запрос можно ответить с помощью набора колокационных осколков, которые содержат данные для этого конкретного арендатора. Один узл PostgreS'L может ответить на запрос за один шаг.

![Улучшенный запрос](media/concepts-hyperscale-colocation/colocation-better-query.png)

В некоторых случаях запросы и схемы таблицы должны быть изменены, чтобы включить идентификатор клиента в уникальные ограничения и условия соединения. Это изменение обычно просто.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как данные о клиентах сопосамы в [учебнике по нескольким тентонам](tutorial-design-database-hyperscale-multi-tenant.md)— это руководство.
