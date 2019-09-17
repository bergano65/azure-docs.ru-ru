---
title: Формат журнала аудита базы данных SQL | Документация Майкрософт
description: Узнайте, как структурированы журналы аудита базы данных SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 334d277370bb8d6678679c887f6a2b89d65652c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569473"
---
# <a name="sql-database-audit-log-format"></a>Формат журнала аудита базы данных SQL

[Аудит базы данных SQL Azure](sql-database-auditing.md) отслеживает события базы данных и записывает их в журнал аудита в учетной записи хранения Azure или отправляет их в концентратор событий или log Analytics для последующей обработки и анализа.

## <a name="naming-conventions"></a>Соглашения об именовании

### <a name="blob-audit"></a>Аудит больших двоичных объектов

Журналы аудита, хранящиеся в хранилище BLOB-объектов, хранятся `sqldbauditlogs` в контейнере с именем в учетной записи хранения Azure. Иерархия каталогов в контейнере имеет форму `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Формат имени BLOB-объекта `<CreationTime>_<FileNumberInSession>.xel`—, `CreationTime` где имеет формат `hh_mm_ss_ms` UTC, а `FileNumberInSession` — это выполняемый индекс в случае, если журналы сеансов охватывают несколько файлов больших двоичных объектов.

Например, для базы данных `Database1` `Server1` можно использовать следующий путь:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Концентратор событий

События аудита записываются в пространство имен и концентратор событий, которые были определены во время настройки аудита, и фиксируются в теле событий [Apache Avro](https://avro.apache.org/) и хранятся с помощью форматирования JSON в кодировке UTF-8. Для чтения журналов аудита можете использовать [средства Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) или похожие средства, которые поддерживают данный формат.

### <a name="log-analytics"></a>Log Analytics

События аудита записываются в log Analytics рабочую область, определенную во время `AzureDiagnostics` настройки аудита, в `SQLSecurityAuditEvents`таблицу с категорией. Дополнительную полезную информацию о языке поиска и командах Log Analytics см. в [документации по поиску Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Поля журнала аудита

| Имя (BLOB-объект) | Имя (концентраторы событий/Log Analytics) | Описание | Тип BLOB-объекта | Тип концентраторов событий и Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Идентификатор действия | varchar (4) | строка |
| action_name | action_name_s | Имя действия | Н/Д | строка |
| additional_information | additional_information_s | Дополнительные сведения о событии, хранящиеся в формате XML | nvarchar (4000) | строка |
| affected_rows | affected_rows_d | Число строк, затронутых запросом | bigint | ssNoversion |
| application_name | application_name_s| Имя клиентского приложения | nvarchar(128) | строка |
| audit_schema_version | audit_schema_version_d | Всегда 1 | ssNoversion | ssNoversion |
| class_type | class_type_s | Тип проверяемой сущности, на которой выполняется аудит | varchar (2) | строка |
| class_type_desc | class_type_description_s | Описание проверяемой сущности, на которой выполняется аудит | Н/Д | строка |
| client_ip | client_ip_s | Исходный IP-адрес клиентского приложения | nvarchar(128) | строка |
| connection_id | Н/Д | Идентификатор соединения на сервере | GUID | Н/Д |
| data_sensitivity_information | data_sensitivity_information_s | Типы сведений и метки чувствительности, возвращаемые отслеживаемым запросом, на основе классифицированных столбцов в базе данных. Дополнительные сведения об [обнаружении и классификации данных в базе данных SQL Azure](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | строка |
| database_name | database_name_s | Контекст базы данных, в котором произошло действие | имеет sysname | строка |
| database_principal_id | database_principal_id_d | Идентификатор контекста пользователя базы данных, в котором выполняется действие | ssNoversion | ssNoversion |
| database_principal_name | database_principal_name_s | Имя контекста пользователя базы данных, в котором выполняется действие | имеет sysname | строка |
| duration_milliseconds | duration_milliseconds_d | Длительность выполнения запроса в миллисекундах | bigint | ssNoversion |
| event_time | event_time_t | Дата и время срабатывания проверяемого действия | datetime2 | datetime |
| HOST_NAME | Н/Д | Имя узла клиента | строка | Н/Д |
| is_column_permission | is_column_permission_s | Флаг, указывающий, является ли это разрешение на уровне столбца. 1 = true, 0 = false | bit | строка |
| Н/Д | is_server_level_audit_s | Флаг, указывающий, что этот аудит находится на уровне сервера | Н/Д | строка |
| Идентификатор object_ | object_id_d | Идентификатор сущности, в которой произошел аудит. К ним относятся: серверные объекты, базы данных, объекты базы данных и объекты схемы. 0, если сущность является самим сервером или если аудит не выполняется на уровне объектов | ssNoversion | ssNoversion |
| object_name | object_name_s | Имя сущности, в которой произошел аудит. К ним относятся: серверные объекты, базы данных, объекты базы данных и объекты схемы. 0, если сущность является самим сервером или если аудит не выполняется на уровне объектов | имеет sysname | строка |
| permission_bitmask | permission_bitmask_s | Если применимо, отображаются разрешения, которые были предоставлены, запрещены или отозваны. | varbinary (16) | строка |
| response_rows | response_rows_d | Число строк, возвращенных в результирующем наборе | bigint | ssNoversion |
| schema_name | schema_name_s | Контекст схемы, в котором произошло действие. Значение NULL для аудитов, происходящих за пределами схемы | имеет sysname | строка |
| Н/Д | securable_class_type_s | Защищаемый объект, сопоставляемый с class_type, подлежит аудиту | Н/Д | строка |
| sequence_group_id | sequence_group_id_g | Уникальный идентификатор | varbinary | GUID |
| sequence_number | sequence_number_d | Отслеживает последовательность записей в одной записи аудита, которая слишком велика для размещения в буфере записи для аудита | ssNoversion | ssNoversion |
| server_instance_name | server_instance_name_s | Имя экземпляра сервера, на котором произошел аудит | имеет sysname | строка |
| server_principal_id | server_principal_id_d | Идентификатор контекста имени входа, в котором выполняется действие | ssNoversion | ssNoversion |
| server_principal_name | server_principal_name_s | Текущее имя входа | имеет sysname | строка |
| server_principal_sid | server_principal_sid_s | Текущий идентификатор безопасности входа | varbinary | строка |
| проблем | session_id_d | Идентификатор сеанса, в котором произошло событие | smallint | ssNoversion |
| session_server_principal_name | session_server_principal_name_s | Участник сервера для сеанса | имеет sysname | строка |
| инструкция | statement_s | Выполненная инструкция T-SQL (при наличии) | nvarchar (4000) | строка |
| успешно | succeeded_s | Указывает, было ли действие, вызвавшее событие, прошло. Для событий, отличных от имени для входа и пакетной службы, он сообщает только о том, успешно ли выполнена проверка разрешений, а не на выполнение операции. 1 = успешное завершение, 0 = сбой | bit | строка |
| target_database_principal_id | target_database_principal_id_d | Участник базы данных, для которого выполняется операция GRANT/DENY/REVOKE. 0, если неприменимо | ssNoversion | ssNoversion |
| target_database_principal_name | target_database_principal_name_s | Целевой пользователь действия. NULL, если неприменимо | строка | строка |
| target_server_principal_id | target_server_principal_id_d | Участник сервера, для которого выполняется операция GRANT/DENY/REVOKE. Возвращает 0, если неприменимо | ssNoversion | ssNoversion |
| target_server_principal_name | target_server_principal_name_s | Целевое имя входа действия. NULL, если неприменимо | имеет sysname | строка |
| target_server_principal_sid | target_server_principal_sid_s | Идентификатор безопасности целевого имени входа. NULL, если неприменимо | varbinary | строка |
| transaction_id | transaction_id_d | Только SQL Server (начиная с 2016)-0 для базы данных SQL Azure | bigint | ssNoversion |
| user_defined_event_id | user_defined_event_id_d | Определяемый пользователем идентификатор события, передаваемый в качестве аргумента в sp_audit_write. NULL для системных событий (по умолчанию) и ненулевое значение для определяемого пользователем события. Дополнительные сведения см. в разделе [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) . | smallint | ssNoversion |
| user_defined_information | user_defined_information_s | Определяемые пользователем сведения, передаваемые в качестве аргумента в sp_audit_write. NULL для системных событий (по умолчанию) и ненулевое значение для определяемого пользователем события. Дополнительные сведения см. в разделе [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) . | nvarchar (4000) | строка |

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об [аудите базы данных SQL Azure](sql-database-auditing.md).