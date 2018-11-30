---
title: Статья об известных проблемах и ограничениях при сетевых миграциях в Базу данных Azure для MySQL | Документация Майкрософт
description: Узнайте об известных проблемах и ограничениях при сетевых миграциях в Базу данных Azure для MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: b83c889e72acb320c308c3ad5ee6243e715fd523
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282882"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Известные проблемы и ограничения при сетевых миграциях в базу данных Azure для PostgreSQL

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из PostgreSQL в базу данных Azure для PostgreSQL. 

## <a name="online-migration-configuration"></a>Настройка сетевой миграции
- На исходном сервере должен использоваться PostgreSQL версии 9.5.11, 9.6.7, 10.3 или более поздней. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](../postgresql/concepts-supported-versions.md).
- Поддерживаются только миграции на ту же версию. Например, миграция PostgreSQL 9.5.11 в базу данных Azure для PostgreSQL 9.6.7 не поддерживается.
- Чтобы включить логическую репликацию в **исходном файле PostgreSQL postgresql.conf**, задайте следующие параметры:
    - **wal_level** = logical
    - **max_replication_slots** = [максимальное количество баз данных для миграции]. Если необходимо перенести 4 базы данных, укажите значение 4.
    - **max_wal_senders** = [количество одновременно выполняющихся баз данных]; рекомендуемое значение — 10
- Добавьте IP-адрес агента DMS в исходный файл PostgresSQL pg_hba.conf.
    1. Запишите IP-адрес DMS после того, как завершите подготовку экземпляра DMS.
    2. Добавьте IP-адрес в файл pg_hba.conf, как показано ниже.

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- У пользователя должны быть разрешения суперпользователя на сервере, на котором размещается база данных-источник.
- За исключением того, что в схеме базы данных-источника не должно быть типа данных ENUM, схемы база данных-источник и целевой базы данных должны совпадать.
- Схема целевой базы данных Azure для PostgreSQL не должна содержать внешних ключей. Чтобы удалить внешние ключи, используйте следующий запрос:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса.

- Схема целевой базы данных Azure для PostgreSQL не должна содержать триггеров. Чтобы отключить триггеры в целевой базе данных, используйте следующий запрос:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Ограничения типа данных

- **Ограничение**: миграция завершится ошибкой во время непрерывной синхронизации, если в базе данных-источнике PostgreSQL присутствует тип данных ENUM.

    **Возможное решение**: в базе данных Azure для PostgreSQL измените тип данных ENUM на тип character varying.

- **Ограничение**: непрерывная синхронизация завершится ошибкой, если в таблицах нет первичного ключа.

    **Возможное решение**: временно задайте первичный ключ для перемещаемой таблицы, чтобы продолжить. После завершения перемещения данных этот ключ можно удалить.

## <a name="lob-limitations"></a>Ограничения больших объектов
Столбцы больших объектов (LOB) — это столбцы, которые могут увеличиваться в размере. Для PostgreSQL примерами типов данных LOB могут служить XML, JSON, IMAGE, TEXT и т. д.

- **Ограничение**: миграция завершится ошибкой, если типы данных LOB используются в качестве первичных ключей.

    **Возможное решение**: замените первичный ключ другими типами данных или столбцами, которые не относятся к LOB.

- **Ограничение**: данные могут быть усечены на целевом объекте, если длина столбца больших объектов (LOB) превышает 32 КБ. Вы можете проверить длину столбца LOB с помощью этого запроса:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Возможное решение**: если у вас есть объект LOB, размер которого превышает 32 КБ, обратитесь в техническую службу по адресу [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

- **Ограничение**: если в таблице есть столбцы LOB и для нее не задан первичный ключ, данные для таблицы могут быть не перенесены.

    **Возможное решение**: временно задайте первичный ключ для перемещаемой таблицы, чтобы продолжить. После завершения перемещения данных этот ключ можно удалить.

## <a name="postgresql10-workaround"></a>Возможное решение для PostgreSQL10
В PostgreSQL 10.x вносятся различные изменения в имена папок pg_xlog, из-за которых миграция может протекать не так, как ожидалось. При миграции из PostgreSQL 10.x в базу данных Azure для PostgreSQL 10.3 выполните приведенный ниже скрипт в базе данных-источнике PostgreSQL, чтобы создать функцию-оболочку для функций pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>Другие ограничения
- Имя базы данных не может содержать точку с запятой (;).
- Строка пароля, которая содержит открывающие и закрывающие фигурные скобки {  }, не поддерживается. Это ограничение применяется при подключении как к базе данных-источнику PostgreSQL, так и к целевой базе данных Azure для PostgreSQL.
- Записываемая таблица должна иметь первичный ключ. Если в таблице нет первичного ключа, результат операций записи DELETE и UPDATE будет непредсказуемым.
- Обновление сегмента первичного ключа игнорируется. Применение такого обновления будет определяться целевой базой данных как обновление, которое не привело к изменению строк, в результате чего запись будет добавлена в таблицу исключений.
- Миграция нескольких таблиц с одинаковыми именами, различающимися только регистром символов (например, таблица1, ТАБЛИЦА1 и Таблица1), может привести к непредсказуемым результатам и поэтому не поддерживается.
- Обработка изменений инструкций DDL для работы с таблицами [CREATE | ALTER | DROP] поддерживается, если они не содержатся в теле внутренней функции или процедуры либо в других вложенных конструкциях. Например, следующее изменение не будет зафиксировано:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Обработка изменений (непрерывная синхронизация) операций TRUNCATE не поддерживается. Миграция секционированных таблиц не поддерживается. При обнаружении секционированной таблицы происходит следующее:
    - База данных сообщает список родительских и дочерних таблиц.
    - В целевой базе данных будет создана обычная таблица с теми же свойствами, что и у выбранных таблиц.
    - Если родительская таблица в базе данных-источнике имеет то же значение первичного ключа, что и у ее дочерних таблиц, произойдет ошибка "повторяющийся ключ".
- В DMS в рамках одной операции можно перенести максимум четыре базы данных.