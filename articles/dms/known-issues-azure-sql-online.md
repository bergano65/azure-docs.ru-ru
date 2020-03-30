---
title: "Известные проблемы: Онлайн-миграция в базу данных S'L"
titleSuffix: Azure Database Migration Service
description: Узнайте об известных проблемах/ограничениях миграции с помощью онлайн-миграций в базу данных Azure S'L с помощью миграционной службы базы данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650781"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Известные проблемы/ограничения миграции с миграцией в интернет-сети в базу данных Azure S'L

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из SQL Server в Базу данных SQL Azure.

> [!IMPORTANT]
> При миграции онлайн-сервера в базу данных Azure S'L не поддерживается миграция SQL_variant типов данных.

### <a name="migration-of-temporal-tables-not-supported"></a>Перемещение темпоральных таблиц не поддерживается

**Симптом**

Если база данных-источник состоит из одной или нескольких темпоральных таблиц, перемещение базы данных завершится сбоем во время операции полной загрузки данных и вы увидите следующее сообщение:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Примеры ошибок темпоральной таблицы](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Решение**

Выполните следующие действия.

1. Найдите темпоральные таблицы в схеме источника, используя приведенный ниже запрос.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.

3. Перезапустите действие миграции.

**Ресурсы**

Дополнительные сведения см. в статье [Темпоральные таблицы](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>При перемещении таблиц включается один или несколько столбцов с типом данных hierarchyid

**Симптом**

Вы можете увидеть исключение SQL, сообщающее, что ntext несовместим с hierarchyid во время операции полной загрузки данных:

![Пример ошибок hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Решение**

Выполните следующие действия.

1. Найдите таблицы пользователя, включающие столбцы с типом данных hierarchyid, используя приведенный ниже запрос.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.

3. Перезапустите действие миграции.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Сбои миграции с различными нарушениями целостности при активных триггерах в схеме во время операции полной загрузки данных или добавочной синхронизации данных

**Решение**

Выполните следующие действия.

1. В базе данных-источнике найдите триггеры, которые сейчас активны, используя приведенный ниже запрос:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Отключите триггеры в вашей базе данных-источнике, выполнив действия, описанные в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Перезапустите действие миграции.

### <a name="support-for-lob-data-types"></a>Поддержка типов данных LOB

**Симптом**

Данные могут быть усечены в целевом объекте, если длина столбца больших объектов (LOB) превышает 32 КБ. Вы можете проверить длину столбца LOB с помощью приведенного ниже запроса:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Решение**

Если у вас есть столбец LOB, который больше 32 кБ, свяжитесь с командой инженеров [в Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="issues-with-timestamp-columns"></a>Проблемы со столбцами меток времени

**Симптом**

Миграционная служба базы данных Azure не мигрирует со значением исходной метки; вместо этого служба миграции базы данных Azure создает новое значение метки времени в целевой таблице.

**Решение**

Если вам нужна миграционная служба базы данных Azure для переноса точного значения временной метки, хранящегося в исходной таблице, обратитесь в инженерную группу [ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Ошибки миграции данных не предоставляют дополнительных сведений о лопасти подробного состояния базы данных

**Симптом**

При сбоях в сбоях миграции в представлении статуса данных сведения о базах данных, выбор ссылки **на ошибки миграции данных** на верхней ленте может не предоставить дополнительные сведения, относящиеся к сбоям миграции.

![пример ошибок перемещения данных: нет сведений](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Решение**

Чтобы получить конкретные сведения о сбое, используйте следующие шаги.

1. Закройте колонку подробностей о состоянии базы данных, чтобы отобразить экран действия миграции.

     ![экран действия миграции](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Щелкните **См. сведения об ошибке**, чтобы увидеть сообщения об ошибках миграции, которые помогут вам устранить их.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Тип данных географии, не поддерживаемый в онлайн-миграции S'LDB

**Симптом**

Миграция завершается субой с сообщением об ошибке, содержащим следующий текст:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Решение**

В то время как служба миграции базы данных Azure поддерживает тип данных географии для автономных миграций в базу данных Azure S'L, для онлайн-миграций тип данных географии не поддерживается. Попробуйте альтернативные методы, чтобы изменить тип данных в источнике на поддерживаемый тип, прежде чем пытаться использовать службу миграции базы данных Azure для онлайн-миграции этой базы данных.

### <a name="supported-editions"></a>Поддерживаемые выпуски

**Симптом**

Миграция завершается субой с сообщением об ошибке, содержащим следующий текст:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Решение**

Поддержка миграций в интернет-сети в базу данных Azure S'L с помощью службы миграции баз данных Azure распространяется только на издания Enterprise, Standard и Developer. Убедитесь, что перед началом процесса миграции вы используете поддерживаемое издание.
