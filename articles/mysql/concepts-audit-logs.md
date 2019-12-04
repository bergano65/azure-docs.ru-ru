---
title: Журналы аудита — база данных Azure для MySQL
description: Описание журналов аудита, доступных в базе данных Azure для MySQL, и доступных параметров для включения уровней ведения журнала.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ea536742b6481cb06fbd3130279ca5d08ba1bc08
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773574"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Журналы аудита в базе данных Azure для MySQL

В базе данных Azure для MySQL журнал аудита доступен пользователям. Журнал аудита можно использовать для наблюдения за действиями на уровне базы данных и обычно используется для обеспечения соответствия.

> [!IMPORTANT]
> Функции журнала аудита в настоящее время доступны в предварительной версии.

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

По умолчанию журнал аудита отключен. Чтобы включить его, установите для параметра `audit_log_enabled` значение Вкл.

Вы можете настроить еще несколько параметров.

- `audit_log_events`: управляет событиями, регистрируемыми в журнале. Конкретные события аудита см. в таблице ниже.
- `audit_log_include_users`: пользователи MySQL, включаемые в ведение журнала. Значение по умолчанию для этого параметра — пустое, которое будет включать всех пользователей для ведения журнала. Это имеет более высокий приоритет, чем `audit_log_exclude_users`. Максимальная длина параметра — 512 символов.
> [!Note]
> `audit_log_include_users` имеет более высокий приоритет, чем `audit_log_exclude_users` например, если audit_log_include_users = `demouser` и audit_log_exclude_users = `demouser`, он будет выполнять аудит журналов, так как `audit_log_include_users` имеет более высокий приоритет.
- `audit_log_exclude_users`: пользователи MySQL, исключаемые из журнала. Максимальная длина параметра — 512 символов.

> [!Note]
> Для `sql_text`журнал будет обрезан, если его длина превышает 2048 символов.

| **Событие** | **Описание** |
|---|---|
| `CONNECTION` | — Инициация подключения (успешно или неуспешно); <br> — Повторное применение проверки подлинности пользователя с другим пользователем или паролем во время сеанса <br> — Завершение подключения |
| `DML_SELECT`| Выбор запросов |
| `DML_NONSELECT` | Операции вставки, удаления и обновления |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Такие запросы, как "DROP DATABASE" |
| `DCL` | Запросы, например "предоставление разрешения" |
| `ADMIN` | Такие запросы, как "ОТОБРАЗИТЬ состояние" |
| `GENERAL` | Все в DML_SELECT, DML_NONSELECT, DML, DDL, ДКЛ и ADMIN |
| `TABLE_ACCESS` | — Доступно только для MySQL 5,7 <br> — Инструкции чтения таблицы, такие как SELECT или INSERT INTO... МЕТЬТЕ <br> — Инструкции DELETE таблицы, такие как DELETE или TRUNCATE TABLE <br> — Табличные инструкции INSERT, такие как INSERT или Replace <br> -Табличные инструкции UPDATE, например UPDATE |

## <a name="access-audit-logs"></a>Доступ к журналам аудита

Журналы аудита интегрируются с журналами диагностики Azure Monitor. После включения журналов аудита на сервере MySQL их можно отправить в журналы Azure Monitor, концентраторы событий или службу хранилища Azure. Дополнительные сведения о том, как включить журналы диагностики в портал Azure, см. в [статье о портале журналов аудита](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Схемы журналов диагностики

В следующих разделах описываются выходные данные журналов аудита MySQL на основе типа события. Порядок появления выбранных полей зависит от выбранного метода вывода.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT``CHANGE USER` (доступно только для MySQL 5,7) |
| `connection_id_d` | Уникальный идентификатор соединения, созданный MySQL |
| `host_s` | Пустой |
| `ip_s` | IP-адрес клиента, подключающегося к MySQL |
| `user_s` | Имя пользователя, который исполняет запрос |
| `db_s` | Имя базы данных, подключенной к |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="general"></a>Общие сведения

Приведенная ниже схема относится к типам событий GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, ДКЛ и ADMIN.

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
| `event_subclass_s` | `LOG`, `ERROR``RESULT` (доступно только для MySQL 5,6) |
| `event_time` | Время начала запроса в формате UTC (метка времени) |
| `error_code_d` | Код ошибки, если запрос не выполнен. `0` означает отсутствие ошибок |
| `thread_id_d` | Идентификатор потока, выполнившего запрос |
| `host_s` | Пустой |
| `ip_s` | IP-адрес клиента, подключающегося к MySQL |
| `user_s` | Имя пользователя, который исполняет запрос |
| `sql_text_s` | Текст полного запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="table-access"></a>Доступ к таблицам

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
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`или `DELETE` |
| `connection_id_d` | Уникальный идентификатор соединения, созданный MySQL |
| `db_s` | Имя базы данных, к которой осуществлялся доступ |
| `table_s` | Имя таблицы, к которой осуществлялся доступ |
| `sql_text_s` | Текст полного запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка журналов аудита в портал Azure](howto-configure-audit-logs-portal.md)