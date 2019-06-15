---
title: Основные понятия работы со службой "База данных Azure для PostgreSQL"
description: В этой статье приведены советы и рекомендации по настройке серверов службы "База данных Azure для PostgreSQL" и управлению ими.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077475"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Совместное размещение таблицы в базе данных Azure для PostgreSQL — Гипермасштабируемость (Citus) (Предварительная версия)

Совместное размещение означает, сохраняя связанные сведения друг с другом на тех же узлах. Запросы можно перейти, быстро в том случае, когда все необходимые данные будут доступны без сетевой трафик. Совместное размещение связанных данных на разных узлах позволяет запросам для эффективной работы в параллельном режиме на каждом узле.

## <a name="data-colocation-for-hash-distributed-tables"></a>Совместное размещение данных для хэш распределенные таблицы

В Гипермасштабируемого строка сохраняется в сегменте, если хэш-значения в столбце распределения попадает в диапазон сегмента хэша. Сегменты с одинаковым диапазоном хэша, всегда размещаются на одном узле. Строки с равного распределения значений столбцов, всегда на одном узле между таблицами.

![Сегменты](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Практический пример использования среды для совместной работы

Рассмотрим следующие таблицы, которые могут быть частью нескольких клиентов web analytics SaaS:

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

Теперь нам нужно отвечать на запросы, которые могут быть выданы панели мониторинга для клиентов, таких как: «Возвращать число посещений за последнюю неделю для всех страниц, начиная с "/ блог" в клиенте шести.»

Если данные в параметр односерверного развертывания, это можно легко выразить нашего запроса, используя широкий набор реляционных операций, предоставляемых SQL:

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

Поскольку [рабочего набора](https://en.wikipedia.org/wiki/Working_set) для этого запроса в памяти, таблицу одним сервером, не. Тем не менее давайте рассмотрим возможности масштабирования модели данных с параметром Гипермасштабируемого развертывания.

### <a name="distributing-tables-by-id"></a>Распределение таблиц по Идентификатору

Запросы на одном сервере запустите замедляет по мере роста количества клиентов и данные, сохраняемые для каждого клиента. Рабочий набор останавливается, изменение размера в памяти и ЦП становится узким местом.

В этом случае мы можем сегментов данных по нескольким узлам, с помощью Гипермасштабируемого. Первый и самый важный выбор, необходимо принять при сегментирования является столбцом распределения. Давайте начнем с упрощенным выбрать `event_id` для таблицы событий и `page_id` для `page` таблицы:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

При данных будет распределена по различных рабочих ролях, мы не может выполнить соединение, так как мы бы на одном узле PostgreSQL. Вместо этого необходимо выдать два запроса:

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

После этого результаты из двух шагов должны объединяться с помощью приложения.

Данных в сегментах, разбиты на узлах необходимо ознакомиться с помощью запросов.

![неэффективные запросы](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

В этом случае распределение данных создает существенные недостатки:

-   Издержки запрашивать каждый сегмент, выполнение нескольких запросов
-   Затраты на Q1 возвращаются к клиенту множество строк
-   Q2 становится большой
-   Необходимость в написании запросов в несколько этапов требует внесения изменений в приложение

Так как данные распределенными, запросы может выполняться параллельно. Тем не менее это полезно только если объем работы, который выполняет запрос значительно больше, чем затраты на запросы нескольким сегментам.

### <a name="distributing-tables-by-tenant"></a>Распределение таблиц по клиентам

В ИТ строки с одинаковым значением столбца распространения гарантированно на одном узле. Начинаю заново, мы можем создать наши таблицы с `tenant_id` столбцом распределения.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Теперь Гипермасштабируемого могут отвечать на исходный запрос одним сервером без изменений (Q1).

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

Из-за фильтра и соединения на tenant_id Гипермасштабируемого знает, что весь запрос может получать ответ с помощью набора совместно размещены сегментов, содержащих данные для этого конкретного клиента. Один узел PostgreSQL можно ответить на запрос за один шаг.

![лучше запроса](media/concepts-hyperscale-colocation/colocation-better-query.png)

В некоторых случаях запросы и схемы таблиц необходимо перевести входит идентификатор клиента в уникальные ограничения и условиях соединения. Тем не менее обычно это просто изменение.

## <a name="next-steps"></a>Дальнейшие действия

- См. в разделе, как совместного размещения данных клиента в [руководстве несколькими клиентами](tutorial-design-database-hyperscale-multi-tenant.md)
