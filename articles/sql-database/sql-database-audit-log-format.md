---
title: Формат журналов аудита
description: Поймите, как структурированы журналы аудита базы данных S'L.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722090"
---
# <a name="sql-database-audit-log-format"></a>Формат журнала о проверке базы данных

[Аудит базы данных Azure S'L](sql-database-auditing.md) отслеживает события базы данных и записывает их в журнал аудита в учетной записи хранения Azure или отправляет их в центр событий или аналитику журналов для обработки и анализа ниже по течению.

## <a name="naming-conventions"></a>Соглашения об именах

### <a name="blob-audit"></a>Blob Аудит

Системы аудита, хранящиеся в `sqldbauditlogs` хранилище Blob, хранятся в контейнере, названном в учетной записи хранилища Azure. Иерархия каталога в контейнере имеет `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`форму. Формат файла Blob `<CreationTime>_<FileNumberInSession>.xel`находится `CreationTime` в формате UTC `hh_mm_ss_ms` и `FileNumberInSession` является запущенным индексом в регистрах сеансов, пронизывающим несколько файлов Blob.

Например, для `Database1` `Server1` базы данных на следующем является возможным допустимым способом:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Только для чтения реплики](sql-database-read-scale-out.md) Аудиторские журналы хранятся в одном контейнере. Иерархия каталога в контейнере имеет `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`форму. Файл blob имеет один и тот же формат. В одном контейнере хранятся журналы аудита только для чтения реплик.


### <a name="event-hub"></a>Концентратор событий

События аудита записываются в пространство имен и концентратор событий, который был определен при конфигурации аудита, и фиксируются в теле событий [Apache Avro](https://avro.apache.org/) и хранятся с помощью форматирования JSON с кодированием UTF-8. Для чтения журналов аудита можете использовать [средства Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) или похожие средства, которые поддерживают данный формат.

### <a name="log-analytics"></a>Log Analytics

События аудита записываются в рабочее пространство Log `AzureDiagnostics` Analytics, `SQLSecurityAuditEvents`определяемое в конфигурации аудита, в таблицу с категорией. Дополнительную полезную информацию о языке поиска и командах Log Analytics см. в [документации по поиску Log Analytics](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Полей журнала аудита

| Имя (Blob) | Имя (Концентраторы событий/аналитика журналов) | Описание | Тип большого двоичного объекта | Концентраторы событий/Тип аналитики журналов |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Идентификатор действия. | varchar(4) | строка |
| action_name | action_name_s | Название акции | Недоступно | строка |
| additional_information | additional_information_s | Любая дополнительная информация о событии, хранящаяся как XML | nvarchar(4000) | строка |
| affected_rows | affected_rows_d | Количество строк, затронутых запросом | BIGINT | INT |
| application_name | application_name_s| Название клиентского приложения | NVARCHAR(128) | строка |
| audit_schema_version | audit_schema_version_d | Всегда 1 | INT | INT |
| class_type | class_type_s | Тип проверяемой сущности, на которую проводится аудит | varchar(2) | строка |
| class_type_desc | class_type_description_s | Описание проверяемой организации, на которую проводится аудит | Недоступно | строка |
| client_ip | client_ip_s | Источник IP клиентского приложения | NVARCHAR(128) | строка |
| connection_id | Недоступно | Идентификатор соединения на сервере | GUID | Недоступно |
| data_sensitivity_information | data_sensitivity_information_s | Типы информации и метки чувствительности, возвращенные проверенным запросом, на основе классифицированных столбцов в базе данных. Узнайте больше о [данных базы данных Лазурного ИЗл](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | строка |
| database_name | database_name_s | Контекст базы данных, в котором произошло действие | sysname | строка |
| database_principal_id | database_principal_id_d | Идентификатор контекста пользователя базы данных, в которых выполняется действие в | INT | INT |
| database_principal_name | database_principal_name_s | Имя контекста пользователя базы данных, в котором выполняется действие | sysname | строка |
| duration_milliseconds | duration_milliseconds_d | Продолжительность выполнения запроса в миллисекундах | BIGINT | INT |
| event_time | event_time_t | Дата и время, когда проверяемое действие уволено | datetime2 | DATETIME |
| host_name | Недоступно | Имя хозяина клиента | строка | Недоступно |
| is_column_permission | is_column_permission_s | Флаг, обозначающий разрешение уровня столбца. 1 - правда, 0 - ложный | bit | строка |
| Недоступно | is_server_level_audit_s | Пометить, если этот аудит находится на уровне сервера | Недоступно | строка |
| object_ id | object_id_d | Идентификатор сущности, над которой выполнен аудит. Это включает в себя: объекты сервера, базы данных, объекты базы данных и объекты схемы. 0, если сущность является самим сервером или если аудит не выполняется на уровне объекта | INT | INT |
| object_name | object_name_s | Имя сущности, для которой проводился аудит. Это включает в себя: объекты сервера, базы данных, объекты базы данных и объекты схемы. 0, если сущность является самим сервером или если аудит не выполняется на уровне объекта | sysname | строка |
| permission_bitmask | permission_bitmask_s | Когда применимо, отображаются предоставленные, запрещенные или отмененные разрешения. | варбинар (16) | строка |
| response_rows | response_rows_d | Количество строк, возвращенных в наборе результатов | BIGINT | INT |
| schema_name | schema_name_s | Контекст схемы, в котором выполнялось действие. NULL для аудитов, происходящих вне схемы | sysname | строка |
| Недоступно | securable_class_type_s | Проверяемый объект, который отображается на class_type | Недоступно | строка |
| sequence_group_id | sequence_group_id_g | Уникальный идентификатор | varbinary | GUID |
| sequence_number | sequence_number_d | Отслеживает последовательность записей в рамках одной аудиторской записи, которая была слишком большой, чтобы поместиться в буфер записи для аудита | INT | INT |
| server_instance_name | server_instance_name_s | Название экземпляра сервера, где произошел аудит | sysname | строка |
| server_principal_id | server_principal_id_d | Идентификатор контекста входа, в котором выполняется действие | INT | INT |
| server_principal_name | server_principal_name_s | Текущий логин | sysname | строка |
| server_principal_sid | server_principal_sid_s | Текущий логин SID | varbinary | строка |
| session_id | session_id_d | Идентификатор сеанса, на котором произошло событие | smallint | INT |
| session_server_principal_name | session_server_principal_name_s | Серверный принцип для сеанса | sysname | строка |
| инструкция | statement_s | Заявление T-S'L, которое было выполнено (если таковое) | nvarchar(4000) | строка |
| succeeded | succeeded_s | Показывает, было ли успешным действие, запустившее событие. Для событий, помимо входа и пакета, это только сообщает ли проверка разрешения удалось или не удалось, а не операции. 1 - успех, 0 - неудача | bit | строка |
| target_database_principal_id | target_database_principal_id_d | Участник базы данных, над которым выполняется операция GRANT/DENY/REVOKE. 0, если не применимо | INT | INT |
| target_database_principal_name | target_database_principal_name_s | Целевой пользователь действия. NULL, если не применимо | строка | строка |
| target_server_principal_id | target_server_principal_id_d | Участник на уровне сервера, над которым выполняется операция GRANT/DENY/REVOKE. Возвраты 0, если это не применимо | INT | INT |
| target_server_principal_name | target_server_principal_name_s | Целевое имя входа действия. NULL, если не применимо | sysname | строка |
| target_server_principal_sid | target_server_principal_sid_s | Идентификатор безопасности целевого имени входа. NULL, если не применимо | varbinary | строка |
| transaction_id | transaction_id_d | Только сервер S'L (начиная с 2016 г.) - 0 для Azure S'L DB | BIGINT | INT |
| user_defined_event_id | user_defined_event_id_d | Пользователь определил идентификатор события, который передается в качестве аргумента для sp_audit_write. NULL для системных событий (по умолчанию) и ненулевых для событий, определяемых пользователем. Для получения дополнительной информации [sp_audit_write](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) см. | smallint | INT |
| user_defined_information | user_defined_information_s | Пользователь определил информацию, переданную в качестве аргумента для sp_audit_write. NULL для системных событий (по умолчанию) и ненулевых для событий, определяемых пользователем. Для получения дополнительной информации [sp_audit_write](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) см. | nvarchar(4000) | строка |

## <a name="next-steps"></a>Next Steps

Узнайте больше об [аудите базы данных Azure S'L.](sql-database-auditing.md)
