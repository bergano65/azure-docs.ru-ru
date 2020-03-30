---
title: "Известные проблемы: Онлайн-миграция из PostgreS'L в базу данных Azure для PostgreS'L"
titleSuffix: Azure Database Migration Service
description: Узнайте об известных проблемах и ограничениях миграции с помощью онлайн-миграций из PostgreS'L в базу данных AzgreS'L с помощью службы миграции базы данных Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235264"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Известные проблемы/ограничения миграции с миграцией в Интернет е-онлайн-миграции из PostgreS'L в Azure DB для PostgreS'L

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из PostgreSQL в базу данных Azure для PostgreSQL.

## <a name="online-migration-configuration"></a>Настройка сетевой миграции

- Исходный сервер PostgreS'L должен запускать версию 9.4, 9.5, 9.6, 10 или 11. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](../postgresql/concepts-supported-versions.md).
- Поддерживаются только миграции в ту же или более высокую версию. Например, поддерживается миграция PostgreS'L 9.5 в базу данных Azure для PostgreS'L 9.6 или 10, но переход от PostgreS'L 11 к PostgreS'L 9.6 не поддерживается.
- Чтобы включить логическую репликацию в **исходном файле PostgreSQL postgresql.conf**, задайте следующие параметры:
  - **wal_level** и логично
  - **max_replication_slots** - «по крайней мере, максимальное количество баз данных для миграции»; если вы хотите перенести четыре базы данных, установите значение по крайней мере до 4.
  - **max_wal_senders** = [количество одновременно выполняющихся баз данных]; рекомендуемое значение — 10
- Добавление IP-адреса агента DMS к источнику PostgreS'L pg_hba.conf
  1. Запишите IP-адрес DMS после завершения подготовки экземпляра службы миграции базы данных Azure.
  2. Добавьте IP-адрес в файл pg_hba.conf, как показано ниже.

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- Пользователь должен иметь роль REPLICATION на сервере, худелении исходной базы данных.
- Схемы исходной базы данных должны совпадать.
- Схема в целевой базе данных Azure для сервера PostgreS-L не должна иметь иностранных ключей. Чтобы удалить внешние ключи, используйте следующий запрос:

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

- Схема в целевой базе данных Azure для сервера PostgreS-L не должна иметь никаких триггеров. Чтобы отключить триггеры в целевой базе данных, используйте следующий запрос:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Ограничения типа данных

  **Ограничение**: Если на таблицах нет основного ключа, изменения не могут быть синхронизированы с целевой базой данных.

  **Возможное решение**: временно задайте первичный ключ для перемещаемой таблицы, чтобы продолжить. После завершения перемещения данных этот ключ можно удалить.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Ограничения при переходе онлайн из AWS RDS PostgreS'L

При попытке выполнения онлайн-миграции из AWS RDS PostgreS'L в базу данных Azure для PostgreS'L могут возникнуть следующие ошибки.

- **Ошибка**: Значение столбца по умолчанию ''column' в таблице 'таблица' в базе данных ''database' отличается от исходных и целевых серверов. It's '{value on source}' on source and '{value on target}' on target" (Значение по умолчанию столбца "{столбец}" в таблице "{таблица}" в базе данных "{база данных}" на исходном сервере не совпадает с таким же параметром на целевом сервере. Это значение "{значение на исходном сервере}" на исходном сервере и "{значение на целевом сервере}" на целевом).

  **Ограничение**: Эта ошибка возникает, когда значение по умолчанию в схеме столбца отличается между исходной и целевой базами данных.
  **Обход**: Убедитесь, что схема на целевой соответствует схеме на источнике. Для получения подробной информации о мигрирующей схеме обратитесь к [онлайн-миграционной документации Azure PostgreS'L.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Ошибка**: Целевая база данных '«база данных» имеет таблицы «количество таблиц», где в качестве исходной базы данных «база данных» имеет таблицы «количество таблиц». The number of tables on source and target databases should match" (Количество таблиц в целевой базе данных "{база данных}": {количество таблиц}. Количество таблиц в базе данных-источнике "{база данных}": {количество таблиц}. Количество таблиц в базах данных-источнике и целевой базе данных должно совпадать).

  **Ограничение**: Эта ошибка возникает, когда количество таблиц отличается между исходными и целевыми базами данных.

  **Обход**: Убедитесь, что схема на целевой соответствует схеме на источнике. Для получения подробной информации о мигрирующей схеме обратитесь к [онлайн-миграционной документации Azure PostgreS'L.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Ошибка:** Исходная база данных (база данных) пуста.

  **Ограничение**: Эта ошибка возникает, когда исходная база данных пуста. Это, скорее всего, потому, что вы выбрали неправильную базу данных в качестве источника.

  **Обход**: Двойная проверка выбранной для миграции исходной базы данных, а затем повторите попытку.

- **Ошибка:** Целевая база данных (база данных) пуста. Перенесите схему.

  **Ограничение**: Эта ошибка возникает, когда в целевой базе данных нет схемы. Убедитесь, что схема на цели соответствует схеме на источнике.
  **Обход**: Убедитесь, что схема на целевой соответствует схеме на источнике. Для получения подробной информации о мигрирующей схеме обратитесь к [онлайн-миграционной документации Azure PostgreS'L.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

## <a name="other-limitations"></a>Другие ограничения

- Имя базы данных не может содержать точку с запятой (;).
- Записываемая таблица должна иметь первичный ключ. Если в таблице нет первичного ключа, результат операций записи DELETE и UPDATE будет непредсказуемым.
- Обновление сегмента первичного ключа игнорируется. Применение такого обновления будет определяться целевой базой данных как обновление, которое не привело к изменению строк, в результате чего запись будет добавлена в таблицу исключений.
- Миграция нескольких таблиц с одинаковыми именами, различающимися только регистром символов (например, таблица1, ТАБЛИЦА1 и Таблица1), может привести к непредсказуемым результатам и поэтому не поддерживается.
- Обработка изменений в «CREATE» ИЗМЕНИТЬ (АНГ) Падение (КА) DDL таблицы TRUNCATE не поддерживается.
- В миграционной службе базы данных Azure одна миграционная деятельность может вместить до четырех баз данных.
