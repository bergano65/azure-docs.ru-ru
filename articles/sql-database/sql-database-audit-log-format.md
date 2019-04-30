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
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417394"
---
# <a name="sql-database-audit-log-format"></a>Формат журнала аудита базы данных SQL

[Аудит базы данных SQL Azure](sql-database-auditing.md) отслеживает события базы данных и записывать их аудита журнал в учетную запись хранилища Azure и отправляет их в концентратор событий или Log Analytics для последующей обработки и анализа.

## <a name="naming-conventions"></a>Соглашения об именовании

### <a name="blob-audit"></a>Аудит BLOB-объектов

Журналы аудита хранятся в хранилище BLOB-объектов хранятся в контейнере с именем `sqldbauditlogs` в учетной записи хранения Azure. Иерархия каталогов в контейнере имеет форму `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Формат имени файла BLOB-объектов — `<CreationTime>_<FileNumberInSession>.xel`, где `CreationTime` указывается в формате UTC `hh_mm_ss_ms` формат, и `FileNumberInSession` — промежуточный индекса в случае, если сеанс журналы диапазонов в нескольких файлах больших двоичных объектов.

Например, для базы данных `Database1` на `Server1` ниже приведен возможный путь допустимым:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Концентратор событий

События аудита записываются в пространство имен и концентратор событий, была определена во время настройки аудита и регистрируются в теле [Apache Avro](https://avro.apache.org/) события и хранятся в формате JSON с кодировкой UTF-8. Для чтения журналов аудита можете использовать [средства Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) или похожие средства, которые поддерживают данный формат.

### <a name="log-analytics"></a>Log Analytics

События аудита записываются в рабочую область Log Analytics, определенную во время настройки аудита в `AzureDiagnostics` таблицы с категорией `SQLSecurityAuditEvents`. Дополнительную полезную информацию о языке поиска и командах Log Analytics см. в [документации по поиску Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Поля журнала аудита

| Имя (Blob) | Имя (событий концентраторов и Log Analytics) | Описание | Тип BLOB-объекта | События концентраторов/Log Analytics типа |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Идентификатор действия | varchar(4) | string |
| action_name | action_name_s | Имя действия | Н/Д | string |
| additional_information | additional_information_s | Любые дополнительные сведения о событии хранятся в формате XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Число строк, затронутых запросом | bigint | int |
| имя_приложения | application_name_s| Имя клиентского приложения | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Всегда имеет значение 1 | int | int |
| тип_класса | class_type_s | Тип аудита сущности, для которой проводится аудит в | varchar(2) | string |
| class_type_desc | class_type_description_s | Описание для аудита сущности, проводится аудит | Н/Д | string |
| client_ip | client_ip_s | Исходный IP-адрес клиентского приложения | nvarchar(128) | string |
| connection_id | Н/Д | Идентификатор соединения на сервере | GUID | Н/Д |
| data_sensitivity_information | data_sensitivity_information_s | Типы информации и меток конфиденциальности, возвращаемых запросом подвергшиеся аудиту, на основе классифицированных столбцов в базе данных. Дополнительные сведения о [базы данных SQL Azure, данные обнаружения и классификации](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | Контекст базы данных, в которой произошло действие | sysname | string |
| database_principal_id | database_principal_id_d | Идентификатор контекста пользователя базы данных, то действие выполняется в | int | int |
| database_principal_name | database_principal_name_s | Имя контекста пользователя базы данных, в котором выполняется действие | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Длительность выполнения запроса в миллисекундах | bigint | int |
| event_time | event_time_t | Дата и время, когда запускается срабатывания действия | datetime2 | Datetime |
| HOST_NAME | Н/Д | Имя узла клиента | string | Н/Д |
| is_column_permission | is_column_permission_s | Флаг, обозначающий разрешение уровня столбца. 1 = ИСТИНА, 0 = false | bit | string |
| Н/Д | is_server_level_audit_s | Флаг, указывающий, при этом аудита на уровне сервера | Н/Д | string |
| Идентификатор object_ | object_id_d | Идентификатор сущности, на которой проводился аудит. Сюда входят: объекты сервера, базы данных, объекты базы данных и объекты схемы. 0, если сущность является сам сервер или если аудит не выполняется на уровне объекта | int | int |
| object_name | object_name_s | Имя сущности, на которой проводился аудит. Сюда входят: объекты сервера, базы данных, объекты базы данных и объекты схемы. 0, если сущность является сам сервер или если аудит не выполняется на уровне объекта | sysname | string |
| permission_bitmask | permission_bitmask_s | Когда применимо, отображаются разрешения, которые были предоставлено, запрещено или отозвано | varbinary(16) | string |
| response_rows | response_rows_d | Число строк, возвращаемых в результирующем наборе | bigint | int |
| schema_name | schema_name_s | Контекст схемы, в которой произошло действие. Значение NULL для аудитов, выполняемых вне схемы | sysname | string |
| Н/Д | securable_class_type_s | Защищаемый объект, который сопоставляется class_type, для которого выполняется аудит | Н/Д | string |
| sequence_group_id | sequence_group_id_g | Уникальный идентификатор | varbinary | GUID |
| sequence_number | sequence_number_d | Отслеживает последовательность записей в пределах одной записи аудита, слишком велико для размещения в буфере записи для аудитов | int | int |
| server_instance_name | server_instance_name_s | Имя экземпляра сервера, где проводился аудит | sysname | string |
| server_principal_id | server_principal_id_d | Идентификатор контекста имени входа, в котором выполняется действие | int | int |
| server_principal_name | server_principal_name_s | Текущее имя входа | sysname | string |
| server_principal_sid | server_principal_sid_s | Идентификатор безопасности текущего имени входа | varbinary | string |
| session_id | session_id_d | Идентификатор сеанса, в котором произошло событие | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Участник на уровне сервера для сеанса | sysname | string |
| инструкция | statement_s | Инструкции T-SQL, которая была выполнена (если таковые имеются) | nvarchar(4000) | string |
| успешно | succeeded_s | Указывает, успешно ли выполнено действие, которое вызвало событие. Для событий, отличных от имени входа и пакетной службы это только сообщает, успешно ли выполнено не операцию проверки разрешения. 1 = успешное завершение, 0 = неуспешное завершение | bit | string |
| target_database_principal_id | target_database_principal_id_d | Участник базы данных на выполняется операция GRANT/DENY/REVOKE. 0, если не применимо | int | int |
| target_database_principal_name | target_database_principal_name_s | Целевой пользователь действия. Значение NULL, если не применимо | string | string |
| target_server_principal_id | target_server_principal_id_d | Участника на уровне сервера, на которой выполняется операция GRANT/DENY/REVOKE. Возвращает 0, если не применимо | int | int |
| target_server_principal_name | target_server_principal_name_s | Целевое имя входа действия. Значение NULL, если не применимо | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | Идентификатор безопасности целевого имени входа. Значение NULL, если не применимо | varbinary | string |
| transaction_id | transaction_id_d | Только SQL Server (начиная с 2016) - 0 для баз данных SQL Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Идентификатор события передается в качестве аргумента для sp_audit_write определяемый пользователем. Значение NULL для системных событий (по умолчанию) и ненулевой идентификатор для определяемого пользователем события. Дополнительные сведения см. в разделе [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Информация, передаваемая в качестве аргумента sp_audit_write определяемый пользователем. Значение NULL для системных событий (по умолчанию) и ненулевой идентификатор для определяемого пользователем события. Дополнительные сведения см. в разделе [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [аудита базы данных SQL Azure](sql-database-auditing.md).