---
title: Статья об известных проблемах и ограничениях при сетевых миграциях в Базу данных Azure для MySQL | Документация Майкрософт
description: Узнайте об известных проблемах и ограничениях при сетевых миграциях в Базу данных Azure для MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: fc5565ab9e3be21b96ce5aa5a938cf22ec3caeb0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848485"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Известные проблемы и ограничения при сетевых миграциях в Базу данных Azure для MySQL

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из MySQL в Базу данных Azure для MySQL.

## <a name="online-migration-configuration"></a>Настройка сетевой миграции

- На исходном сервере должна быть MySQL версии 5.6.35, 5.7.18 или более поздней.
- База данных Azure для MySQL поддерживает:
  - MySQL Community Edition;
  - подсистему InnoDB.
- Миграция между одинаковыми версиями. Миграция с MySQL 5.6 в Базу данных Azure для MySQL 5.7 не поддерживается.
- Включите ведение двоичных журналов в файле my.ini (Windows) или my.cnf (Unix).
  - Установите для значения Server_id любое число, которое больше или равно 1, например, Server_id = 1 (только для MySQL 5.6).
  - Set log-bin = \<path > (только для MySQL 5,6)
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

Столбцы больших объектов (LOB) — это столбцы, которые могут увеличиваться в размере. Для MySQL, Medium Text, LONGTEXT, BLOB, Медиумблоб, Лонгблоб и т. д. — это некоторые типы данных LOB.

- **Ограничения**: миграция завершится ошибкой, если типы данных LOB используются в качестве первичных ключей.

    **Возможное решение**: замените первичный ключ другими типами данных или столбцами, которые не относятся к LOB.

- **Ограничение**: данные могут быть усечены в целевом объекте, если длина столбца больших объектов (LOB) превышает 32 КБ. Вы можете проверить длину столбца LOB с помощью этого запроса:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Возможное решение**: Если у вас есть объект LOB, размер которого превышает 32 КБ, обратитесь в службу технической поддержки [по адресу](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Ограничения при миграции через Интернет из AWS RDS MySQL

При попытке выполнить миграцию через Интернет из AWS RDS MySQL в базу данных Azure для MySQL могут возникнуть следующие ошибки.

- **Ошибка:** В базе{0}данных "" есть внешние ключи. Исправьте целевой объект и запустите новую процедуру переноса данных. Выполните приведенный ниже скрипт на целевом объекте, чтобы вывести список внешних ключей.

  **Ограничения**: Если у вас есть внешние ключи в схеме, начальная загрузка и непрерывная синхронизация во время миграции завершатся сбоем.
  **Возможное решение**: Выполните следующий скрипт в MySQL Workbench для извлечения скриптов удаления и добавления внешнего ключа:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Ошибка:** База данных{0}"" не существует на сервере. На указанном исходном сервере MySQL учитывается регистр. Проверьте имя базы данных.

  **Ограничения**: Эта ошибка может возникнуть при переносе базы данных MySQL в Azure с помощью интерфейса командной строки (CLI). Службе не удалось найти базу данных на исходном сервере, так как возможно, вы указали неправильное имя базы данных или база данных не существует на указанном сервере. Обратите внимание, что в именах баз данных учитывается регистр.

  **Возможное решение**: Укажите точное имя базы данных и повторите попытку.

- **Ошибка:** В базе данных "{Database}" есть таблицы с одинаковыми именами. В Базе данных Azure для MySQL не учитывается регистр в именах таблиц.

  **Ограничения**: Эта ошибка возникает, когда в базе данных-источнике есть две таблицы с одинаковым именем. База данных Azure для MySQL не поддерживает таблицы с учетом регистра.

  **Возможное решение**: Обновите имена таблиц, чтобы они были уникальными, а затем повторите операцию.

- **Ошибка:** Целевая база данных {Database} пуста. Перенесите схему.

  **Ограничения**: Эта ошибка возникает, когда целевая база данных Azure для базы данных MySQL не имеет требуемой схемы. Миграция схемы необходима для обеспечения миграции данных в целевой объект.

  **Возможное решение**: [Перенесите схему](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) из базы данных источника в целевую базу данных.

## <a name="other-limitations"></a>Другие ограничения

- Строка пароля, которая в начале и конце содержит открывающие и закрывающие фигурные скобки {  }, не поддерживается. Это ограничение применяется при подключении как к исходной базе данных MySQL, так и к целевой базе данных Azure для MySQL.
- Следующие инструкции DDL не поддерживаются:
  - все инструкции DDL, обрабатывающие разделы;
  - инструкция удаления таблицы;
  - Переименование таблицы
- Использование инструкции *alter table <имя_таблицы> add column <имя_столбца>* для добавления столбцов в начало или в середину таблицы не поддерживается. Инструкция *alter table <имя_таблицы> add column <имя_столбца>* добавляет столбец в конец таблицы.
- Индексы, созданные только для части данных столбца, не поддерживаются. Приведенная ниже инструкция представляет пример создания индекса, используя только часть данных столбца:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- В DMS в рамках одной операции можно перенести максимум четыре базы данных.
