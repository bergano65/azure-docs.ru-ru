---
title: Статья об известных проблемах и ограничениях при сетевых миграциях в Базу данных SQL Azure | Документация Майкрософт
description: Узнайте об известных проблемах и ограничениях при сетевых миграциях в Базу данных SQL Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: afafaa86988905329a0e4ff45f29bea9d1d57820
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501045"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Известные проблемы и ограничения миграции при оперативной миграции в базу данных SQL Azure

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из SQL Server в Базу данных SQL Azure.

> [!IMPORTANT]
> Миграция типов данных SQL_variant с помощью оперативной миграции SQL Server в базу данных SQL Azure не поддерживается.

### <a name="migration-of-temporal-tables-not-supported"></a>Перемещение темпоральных таблиц не поддерживается

**Симптом** Если база данных источника состоит из одной или нескольких временных таблиц, то миграция базы данных завершается сбоем во время операции полной загрузки данных и может появиться следующее сообщение:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Примеры ошибок темпоральной таблицы](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Обходной путь** Выполните следующие действия.

1. Найдите темпоральные таблицы в схеме источника, используя приведенный ниже запрос.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.

3. Перезапустите действие миграции.

**Ресурсы** Дополнительные сведения см. в статьях о [временных таблицах](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>При перемещении таблиц включается один или несколько столбцов с типом данных hierarchyid

**Симптом** В ходе операции полной загрузки данных может появиться исключение SQL, предлагающее "ntext несовместим с hierarchyid":

![Пример ошибок hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Обходной путь** Выполните следующие действия.

1. Найдите таблицы пользователя, включающие столбцы с типом данных hierarchyid, используя приведенный ниже запрос.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.

3. Перезапустите действие миграции.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Сбои миграции с различными нарушениями целостности при активных триггерах в схеме во время операции полной загрузки данных или добавочной синхронизации данных

**Обходной путь** Выполните следующие действия.

1. В базе данных-источнике найдите триггеры, которые сейчас активны, используя приведенный ниже запрос:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Отключите триггеры в вашей базе данных-источнике, выполнив действия, описанные в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Перезапустите действие миграции.

### <a name="support-for-lob-data-types"></a>Поддержка типов данных LOB

**Симптом** Если длина столбца больших объектов (LOB) больше 32 КБ, данные могут быть усечены в целевом объекте. Вы можете проверить длину столбца LOB с помощью приведенного ниже запроса:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Обходной путь** Если у вас есть столбец LOB, размер которого превышает 32 КБ, обратитесь к группе разработчиков по адресу запросите [миграцию базы данных Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Проблемы со столбцами меток времени

**Симптом** Azure Database Migration Service не выполняет миграцию исходного значения timestamp; Вместо этого Azure Database Migration Service создает новое значение timestamp в целевой таблице.

**Возможное решение**

Если требуется Azure Database Migration Service для переноса точного значения метки времени, хранящегося в исходной таблице, обратитесь к группе инженеров, [запросив миграцию базы данных Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>В колонке "подробное состояние базы данных" не представлены дополнительные сведения об ошибках переноса данных

**Симптом** При возникновении ошибок миграции в представлении состояние сведений о базах данных выбор ссылки **ошибки переноса данных** на верхней ленте может не дать дополнительных сведений, относящихся к сбоям миграции.

![пример ошибок перемещения данных: нет сведений](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Обходной путь** Чтобы получить сведения о конкретных сбоях, выполните следующие действия.

1. Закройте колонку подробностей о состоянии базы данных, чтобы отобразить экран действия миграции.

     ![экран действия миграции](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Щелкните **См. сведения об ошибке**, чтобы увидеть сообщения об ошибках миграции, которые помогут вам устранить их.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Тип данных geography не поддерживается в оперативной миграции SQLDB

**Симптом** Сбой миграции с сообщением об ошибке, содержащим следующий текст:

 "* * обнаружена неустранимая ошибка", "Ерроревентс":<Table>.<Column> имеет тип "GEOGRAPHY", который не поддерживается "полной нагрузкой" в режиме поддержки "полный бизнес". "

**Обходной путь** Хотя Azure Database Migration Service поддерживает тип данных geography для автономных миграций в базу данных SQL Azure, для оперативной миграции тип данных geography не поддерживается. Попробуйте использовать альтернативные методы для изменения типа данных в источнике на поддерживаемый тип перед использованием Azure Database Migration Service для оперативной миграции этой базы данных.

### <a name="supported-editions"></a>Поддерживаемые выпуски

**Симптом** Сбой миграции с сообщением об ошибке, содержащим следующий текст:

 Ошибка проверки параметров миграции: Выпуск сервера [Business Intelligence Edition (64 – bit)] не соответствует поддерживаемым выпускам [Enterprise, Standard, Developer].

**Обходной путь** Поддержка оперативной миграции в базу данных SQL Azure с помощью Azure Database Migration Service распространяется только на выпуски Enterprise, Standard и Developer. Убедитесь, что вы используете поддерживаемый выпуск, прежде чем начать процесс миграции.
