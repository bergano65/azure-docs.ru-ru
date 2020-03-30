---
title: "Известные проблемы: Онлайн-миграция в базу данных Azure для MyS'L"
titleSuffix: Azure Database Migration Service
description: Узнайте об известных проблемах и ограничениях миграции с помощью онлайн-миграций в базу данных Azure для MyS'L при использовании службы миграции базы данных Azure.
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235278"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Проблемы с миграцией в Интернете & ограничения для Azure DB для MyS'L с помощью службы миграции баз данных Azure

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из MySQL в Базу данных Azure для MySQL.

## <a name="online-migration-configuration"></a>Настройка сетевой миграции


- На исходном сервере должна быть MySQL версии 5.6.35, 5.7.18 или более поздней.
- База данных Azure для MySQL поддерживает:
  - MySQL Community Edition;
  - подсистему InnoDB.
- Миграция между одинаковыми версиями. Миграция с MySQL 5.6 в Базу данных Azure для MySQL 5.7 не поддерживается.
- Включите ведение двоичных журналов в файле my.ini (Windows) или my.cnf (Unix).
  - Установите для значения Server_id любое число, которое больше или равно 1, например, Server_id = 1 (только для MySQL 5.6).
  - Установить log-bin и \<путь> (только для MyS'L 5.6)
  - Установите binlog_format = row.
  - Установите Expire_logs_days = 5 ( рекомендуется только для MySQL 5.6).
- Пользователю должна быть назначена роль ReplicationAdmin.
- Параметры сортировки для исходной базы данных MySQL такие же, как те, что определены в целевой базе данных службы "База данных Azure для MySQL".
- Схемы исходной базы данных MySQL и целевой базы данных в Базе данных Azure для MySQL должны соответствовать.
- Схема целевой базы данных службы "База данных Azure для MySQL" не должна содержать внешних ключей. Чтобы удалить внешние ключи, используйте следующий запрос:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса.
- Схема целевой базы данных службы "База данных Azure для MySQL" не должна содержать триггеров. Чтобы удалить триггеры в целевой базе данных:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Ограничения типа данных

- **Ограничение**: миграция завершится ошибкой во время непрерывной синхронизации, если в исходной базе данных MySQL присутствует тип данных JSON.

    **Возможное решение**: измените тип данных JSON на средний или длинный текст в исходной базе данных MySQL.

- **Ограничение**: непрерывная синхронизация завершится ошибкой, если в таблицах нет первичного ключа.

    **Возможное решение**: временно задайте первичный ключ для перемещаемой таблицы, чтобы продолжить. После завершения перемещения данных этот ключ можно удалить.

## <a name="lob-limitations"></a>Ограничения больших объектов

Столбцы больших объектов (LOB) — это столбцы, которые могут увеличиваться в размере. Для MyS'L, Средний текст, Longtext, Blob, Mediumblob, Longblob и т.д., некоторые из типов данных LOB.

- **Ограничение**: миграция завершится ошибкой, если типы данных LOB используются в качестве первичных ключей.

    **Возможное решение**: замените первичный ключ другими типами данных или столбцами, которые не относятся к LOB.

- **Ограничение**: данные могут быть усечены на целевом объекте, если длина столбца больших объектов (LOB) превышает 32 КБ. Вы можете проверить длину столбца LOB с помощью этого запроса:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Обход:** Если у вас есть LOB объект, который больше, чем 32 КБ, контакт инженерной группы [в Ask Azure миграции базы данных](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Ограничения при переходе онлайн из AWS RDS MyS'L

При попытке выполнения онлайн-миграции из базы данных AWS RDS MyS'L в Базу данных Azure для MyS'L вы можете столкнуться со следующими ошибками.

- **Ошибка:** База{0}данных ' имеет иностранный ключ (ы) на цель. Исправьте целевой объект и запустите новую процедуру переноса данных. Выполните ниже сценарий на цель, чтобы перечислить иностранные ключевые (ы)

  **Ограничение**: Если у вас есть иностранные ключи в вашей схеме, начальная нагрузка и непрерывная синхронизация миграции не удастся.
  **Обходной путь**: Выполните следующий сценарий в myS'L workbench, чтобы извлечь падение иностранного ключевого скрипта и добавить иностранный сценарий ключа:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Ошибка:** База{0}данных ' не существует на сервере. На указанном исходном сервере MySQL учитывается регистр. Проверьте имя базы данных.

  **Ограничение**: При переходе базы данных MyS'L в Azure с помощью интерфейса Command Line (CLI) пользователи могут нажать эту ошибку. Служба не может найти базу данных на исходном сервере, что может быть связано с тем, что вы могли предоставить неправильное имя базы данных или базы данных не существует на указанном сервере. Имена базы данных примечаний чувствительны к случаям.

  **Обходной путь**: Укажите точное имя базы данных, а затем повторите попытку.

- **Ошибка:** В базе данных есть таблицы с тем же именем« «база данных». В Базе данных Azure для MySQL не учитывается регистр в именах таблиц.

  **Ограничение**: Эта ошибка происходит, когда у вас есть две таблицы с одинаковым именем в исходной базе данных. База данных Azure для MyS'L не поддерживает таблицы, чувствительные к случаям.

  **Обход**: Обновление названий таблиц, чтобы быть уникальным, а затем попробуйте еще раз.

- **Ошибка:** Целевая база данных (база данных) пуста. Перенесите схему.

  **Ограничение**: Эта ошибка возникает, когда целевая база данных Azure для базы данных MyS'L не имеет требуемой схемы. Для включения данных в цель требуется миграция данных.

  **Обход**: [Перенос схемы](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) из исходной базы данных в целевую базу данных.

## <a name="other-limitations"></a>Другие ограничения

- Строка пароля, которая в начале и конце содержит открывающие и закрывающие фигурные скобки {  }, не поддерживается. Это ограничение применяется при подключении как к исходной базе данных MySQL, так и к целевой базе данных Azure для MySQL.
- Следующие инструкции DDL не поддерживаются:
  - все инструкции DDL, обрабатывающие разделы;
  - Удаление таблицы
  - Переименование таблицы
- Использование инструкции *alter table <имя_таблицы> add column <имя_столбца>* для добавления столбцов в начало или в середину таблицы не поддерживается. Инструкция *alter table <имя_таблицы> add column <имя_столбца>* добавляет столбец в конец таблицы.
- Индексы, созданные только для части данных столбца, не поддерживаются. Приведенная ниже инструкция представляет пример создания индекса, используя только часть данных столбца:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- В миграционной службе базы данных Azure ограничение для миграции в одном миграционном секторе составляет четыре.

- **Ошибка:** Слишком большой размер строки (> 8126). Изменение некоторых столбцов на TEXT или BLOB может помочь. В текущем формате строки приставка BLOB с 0 байтами хранится в строке.

  **Ограничение**: Эта ошибка происходит при переходе в базу данных Azure для MyS'L с помощью движка хранения InnoDB, и любой размер строки таблицы слишком велик (>8126 байтов).

  **Обход**: Обновление схемы таблицы, которая имеет размер строки больше, чем 8126 байтов. Мы не рекомендуем менять строгий режим, так как данные будут усечены. Изменение page_size не поддерживается.
