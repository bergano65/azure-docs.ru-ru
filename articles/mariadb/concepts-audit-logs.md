---
title: Аудит журналов - База данных Azure для MariaDB
description: Описывает журналы аудита, доступные в базе данных Azure для MariaDB, а также доступные параметры для включения уровней регистрации.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063826"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Аудит журналов в базе данных Azure для MariaDB

В базе данных Azure для MariaDB журнал аудита доступен пользователям. Журнал аудита может использоваться для отслеживания действий на уровне базы данных и обычно используется для обеспечения соответствия требованиям.

> [!IMPORTANT]
> Функциональность журнала аудита в настоящее время находится в предварительном просмотре.

## <a name="configure-audit-logging"></a>Настройка журналов аудита

По умолчанию журнал аудита отключен. Чтобы включить его, установите `audit_log_enabled` НА.

Вы можете настроить еще несколько параметров.

- `audit_log_events`: контролирует регистрированные события. Ниже приведена таблица для конкретных событий аудита.
- `audit_log_include_users`: Пользователи MariaDB должны быть включены для ведения журнала. Значение по умолчанию для этого параметра пусто, которое будет включать в себя всех пользователей для регистрации. Это имеет более `audit_log_exclude_users`высокий приоритет над . Максимальная длина параметра составляет 512 символов.
> [!Note]
> `audit_log_include_users`имеет более `audit_log_exclude_users`высокий приоритет над . Например, `audit_log_include_users`  =  `demouser` если `audit_log_exclude_users`  =  `demouser`пользователь будет включен в журналы `audit_log_include_users` аудита, потому что имеет более высокий приоритет.
- `audit_log_exclude_users`: Пользователи MariaDB будут исключены из регистрации. Позволяет не более четырех пользователей. Максимальная длина параметра составляет 256 символов.

| **Событие** | **Описание** |
|---|---|
| `CONNECTION` | - Инициирование соединения (успешное или неудачное) <br> - Реатаутентация пользователя с другим пользователем/паролем во время сеанса <br> - Прекращение подключения |
| `DML_SELECT`| Запросы SELECT |
| `DML_NONSELECT` | ВОПРОСы INSERT/DELETE/UPDATE |
| `DML` | DML - DML_SELECT DML_NONSELECT |
| `DDL` | Запросы, такие как "DROP DATABASE" |
| `DCL` | Запросы, такие как "GRANT PERMISSION" |
| `ADMIN` | Запросы, такие как "SHOW STATUS" |
| `GENERAL` | Все в DML_SELECT, DML_NONSELECT, DML, DDL, DCL, и ADMIN |

## <a name="access-audit-logs"></a>Доступ к журналам аудита

Аудиторские журналы интегрированы с диагностическими журналами Azure Monitor. После включения журналов аудита на сервере MariaDB можно изложить их в журналы Azure Monitor, концентраторы событий или хранилище Azure. Подробнее о том, как включить диагностические журналы, можно узнать на портале Azure, смотрите [статью портала журналов аудита](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Схемы диагностических журналов

В следующих разделах описывается вывод журналов аудита MariaDB на основе типа события. Порядок появления выбранных полей зависит от выбранного метода вывода.

### <a name="connection"></a>Соединение

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Уникальный идентификатор соединения, генерируемый MariaDB |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключаемого к MariaDB |
| `user_s` | Имя пользователя, выдающего запрос |
| `db_s` | Название базы данных, подключенной к |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="general"></a>Общие сведения

Схема ниже применяется к типам событий GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL и ADMIN.

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Имя сервера |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Секунды начала запроса в метке времени UNIX |
| `error_code_d` | Код ошибки, если запрос не удался. `0`означает отсутствие ошибки |
| `thread_id_d` | Идентификатор потока, выполняемый запросом |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключаемого к MariaDB |
| `user_s` | Имя пользователя, выдающего запрос |
| `sql_text_s` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Анализ журналов в журналах Azure Monitor

После того, как журналы аудита будут переданы в журналы Azure Monitor через диагностические журналы, вы сможете провести дальнейший анализ проверенных событий. Ниже приведены некоторые примеры запросов, которые помогут вам начать работу. Убедитесь в том, чтобы обновить ниже с вашим именем сервера.

- Список событий GENERAL на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Список событий CONNECTION на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Обобщенные проверенные события на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- График распределения типа события аудита на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Список проверенных событий на всех серверах MariaDB с диагностическими журналами, включенными для журналов аудита

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Дальнейшие действия

- [Как настроить журналы аудита на портале Azure](howto-configure-audit-logs-portal.md)