---
title: Журналы аудита для базы данных Azure для MySQL
description: Описание журналов аудита, доступных в базе данных Azure для MySQL и параметры, доступные для включения уровней ведения журнала.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443846"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Журналы аудита в базе данных Azure для MySQL

В базе данных Azure для MySQL журнале аудита доступно для пользователей. Журнал аудита может использоваться для отслеживания действий на уровне базы данных и обычно используется для обеспечения соответствия.

> [!IMPORTANT]
> Функция журнала аудита в настоящий момент в предварительной версии.

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

По умолчанию отключена в журнал аудита. Чтобы ее включить, задайте `audit_log_enabled` в значение ON.

Вы можете настроить еще несколько параметров.

- `audit_log_events`: управляет событий в журнал. См. в следующей таблице для определенных событий аудита.
- `audit_log_exclude_users`: Пользователи MySQL, должны быть исключены из ведения журнала. Позволяет выполнять не более четырех пользователей. Максимальная длина параметра составляет 256 символов.

| **Событие** | **Описание** |
|---|---|
| `CONNECTION` | -Инициации подключения (успешное или неуспешное) <br> -Пользователь повторная проверка подлинности с другой пользователь/пароль во время сеанса <br> -Завершения соединений |
| `DML_SELECT`| Запросы SELECT |
| `DML_NONSELECT` | Запросы INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Запросы, такие как «DROP DATABASE» |
| `DCL` | Запросы, такие как «ПРЕДОСТАВИТЬ разрешение» |
| `ADMIN` | Запросы, такие как «Показать состояние» |
| `GENERAL` | Все это в DML_SELECT, DML_NONSELECT, DML, DDL, ODC-ФАЙЛ и АДМИНИСТРАТОРА |
| `TABLE_ACCESS` | -Доступно только для MySQL 5.7 <br> -Таблица для чтения инструкции, такие как SELECT или INSERT INTO... SELECT <br> -Table delete инструкции, такие как DELETE или TRUNCATE TABLE <br> -Инструкции insert table, таких как вставки или ЗАМЕНЫ <br> -Инструкции update table, такие как UPDATE |

## <a name="access-audit-logs"></a>Доступ к журналам аудита

Журналы аудита интегрированы с помощью журналов диагностики Azure Monitor. После включения журналы аудита на сервере MySQL, можно выдавать их в журналах Azure Monitor, концентраторы событий и службы хранилища Azure. Дополнительные сведения о том, как включить журналы диагностики на портале Azure, см. в разделе [портала статьи в журнале аудита](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Схемы для журналов диагностики

В следующих разделах, что такое выходные данные в журналах аудита MySQL в зависимости от типа события. Порядок появления выбранных полей зависит от выбранного метода вывода.

### <a name="connection"></a>Подключение

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Имя сервера |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (доступно только для MySQL 5.7) |
| `connection_id_d` | Уникальный идентификатор соединения созданные MySQL |
| `host_s` | Пустой |
| `ip_s` | IP-адрес клиента при подключении к MySQL |
| `user_s` | Имя пользователя, выполняющего запрос |
| `db_s` | Имя базы данных, подключенных к |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="general"></a>Общие сведения

Следующая схема применяется к Общие, DML_SELECT, DML_NONSELECT, DML, DDL, ODC-ФАЙЛ и АДМИНИСТРАТОРА типов событий.

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Имя сервера |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (доступно только для версии MySQL 5.6) |
| `event_time` | Запрос запуска секунд в метку времени UNIX |
| `error_code_d` | Код ошибки, если не удалось выполнить запрос. `0` Ошибка не означает, что |
| `thread_id_d` | Идентификатор потока, который выполняет запрос |
| `host_s` | Пустой |
| `ip_s` | IP-адрес клиента при подключении к MySQL |
| `user_s` | Имя пользователя, выполняющего запрос |
| `sql_text_s` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="table-access"></a>Доступ к таблице

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Имя сервера |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, или `DELETE` |
| `connection_id_d` | Уникальный идентификатор соединения созданные MySQL |
| `db_s` | Имя базы данных |
| `table_s` | Имя таблицы, доступ к |
| `sql_text_s` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="next-steps"></a>Дальнейшие действия

- [Как настроить журналы аудита на портале Azure](howto-configure-audit-logs-portal.md)