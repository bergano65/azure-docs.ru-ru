---
title: Использование расширений PostgreSQL
description: Использование расширений PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631772"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Использование расширений PostgreSQL в службе "Дуга Azure", в которой включена PostgreSQLная группа серверов

PostgreSQL лучше использовать с расширениями. На самом деле, ключевым элементом нашей собственной функции масштабирования является расширение, предоставляемое корпорацией Майкрософт, `citus` которое устанавливается по умолчанию, что позволяет postgres прозрачно распределять данные между несколькими узлами.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Список расширений
Помимо расширений в [`contrib`](https://www.postgresql.org/docs/12/contrib.html) , список расширений, имеющихся в контейнерах для группы серверов PostgreSQL в службе "Дуга Azure", включает:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Этот список будет развиваться за сверхурочные работы, и в этом документе будут опубликованы обновления. Вы еще не можете добавить расширения, которые выходят за рамки перечисленных выше.

В этом руководство будет использоваться два из следующих расширений:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Управление расширениями

### <a name="enable-extensions"></a>Включить расширения
Этот шаг не требуется для расширений, входящих в состав `contrib` .
Общий формат команды для включения расширений:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Включить расширение во время создания группы серверов:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Включить расширение в уже существующем экземпляре:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Получить список включенных расширений:
Выполните одну из приведенных ниже команд.

##### <a name="with-azdata"></a>С аздата
```console
azdata arc postgres server show -n <server group name>
```
Прокрутите выходные данные и обратите внимание на разделы енгине\екстенсионс в спецификациях вашей группы серверов. Пример:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>С kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Прокрутите выходные данные и обратите внимание на разделы енгине\екстенсионс в спецификациях вашей группы серверов. Пример:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Создание расширений:
Подключитесь к группе серверов с помощью выбранного клиентского средства и выполните стандартный запрос PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Получите список расширений, созданных в группе серверов.
Подключитесь к группе серверов с помощью выбранного клиентского средства и выполните стандартный запрос PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Удалите расширение из группы серверов:
Подключитесь к группе серверов с помощью выбранного клиентского средства и выполните стандартный запрос PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Использование PostGIS и расширений Pg_cron

### <a name="the-postgis-extension"></a>Расширение PostGIS

Можно либо включить расширение PostGIS в существующую группу серверов, либо создать новую с уже включенным расширением:

**Включение расширения во время создания группы серверов:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Включение расширения в уже существующем экземпляре:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Чтобы проверить, какие расширения установлены, используйте приведенную ниже стандартную команду PostgreSQL после подключения к экземпляру с помощью вашего любимого клиентского средства PostgreSQL, например Azure Data Studio.
```console
select * from pg_extension;
```

Для примера PostGIS сначала получите [демонстрационные данные](http://duspviz.mit.edu/tutorials/intro-postgis/) из подразделения MIT, & планирования. Может потребоваться выполнить команду `apt-get install unzip` , чтобы установить распаковку при использовании виртуальной машины для тестирования.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Давайте подключимся к нашей базе данных и создадим расширение PostGIS:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Если вы хотите использовать одно из расширений в `postgis` пакете (например,, `postgis_raster` ...), необходимо `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` сначала создать расширение PostGIS, а затем создать другое расширение. Например: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

И создайте схему:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Теперь можно объединить PostGIS с функциональностью Scale out, сделав таблицу coffee_shops распределенной:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Давайте загрузим некоторые данные:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

И заполните `geom` поле правильно закодированной широтой и долготой в `geometry` типе данных PostGIS:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Теперь мы можем перечислить кафе, ближайшие к MIT (77 Массачусетс AVE в 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Расширение pg_cron

Давайте развернемся в `pg_cron` нашей группе серверов PostgreSQL в дополнение к PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Обратите внимание, что это приведет к перезапуску узлов и установке дополнительных расширений, что может занять 2-3 минут.

Теперь можно подключиться снова и создать `pg_cron` расширение:

```sql
CREATE EXTENSION pg_cron;
```

В целях тестирования позволяет создать таблицу `the_best_coffee_shop` , которая принимает случайное имя из предыдущей `coffee_shops` таблицы и задает содержимое таблицы:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Можно использовать `cron.schedule` плюс несколько инструкций SQL, чтобы получить случайное имя таблицы (Обратите внимание на использование временной таблицы для хранения результата распределенного запроса) и сохранить его в `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Еще раз в минуту мы получаем другое имя:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Полные сведения о синтаксисе см. в [PG_CRON файле сведений](https://github.com/citusdata/pg_cron) .

>[!NOTE]
>Удаление расширения не поддерживается `citus` . `citus`Расширение является обязательным для обеспечения возможности масштабирования.

## <a name="next-steps"></a>Дальнейшие действия
- Ознакомьтесь с документацией по [plv8](https://plv8.github.io/)
- Ознакомьтесь с документацией по [PostGIS](https://postgis.net/)
- Ознакомьтесь с документацией по [`pg_cron`](https://github.com/citusdata/pg_cron)
