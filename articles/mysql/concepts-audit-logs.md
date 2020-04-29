---
title: Журналы аудита — база данных Azure для MySQL
description: Описание журналов аудита, доступных в базе данных Azure для MySQL, и доступных параметров для включения уровней ведения журнала.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062545"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Журналы аудита в базе данных Azure для MySQL

В базе данных Azure для MySQL журнал аудита доступен пользователям. Журнал аудита можно использовать для наблюдения за действиями на уровне базы данных и обычно используется для обеспечения соответствия.

> [!IMPORTANT]
> Функции журнала аудита в настоящее время доступны в предварительной версии.

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

По умолчанию журнал аудита отключен. Чтобы включить его, задайте `audit_log_enabled` значение ON.

Вы можете настроить еще несколько параметров.

- `audit_log_events`: управляет событиями, регистрируемыми в журнале. Конкретные события аудита см. в таблице ниже.
- `audit_log_include_users`: Пользователи MySQL, включаемые в ведение журнала. Значение по умолчанию для этого параметра — пустое, которое будет включать всех пользователей для ведения журнала. Это имеет более высокий приоритет `audit_log_exclude_users`, чем. Максимальная длина параметра — 512 символов.
> [!Note]
> `audit_log_include_users`имеет более высокий приоритет `audit_log_exclude_users`, чем. Например `audit_log_include_users`  =  `demouser` , если `audit_log_exclude_users`  = и `demouser`, пользователь будет включаться в журналы аудита, так как `audit_log_include_users` имеет более высокий приоритет.
- `audit_log_exclude_users`: Пользователи MySQL, которые будут исключены из ведения журнала. Максимальная длина параметра — 512 символов.

> [!Note]
> Для `sql_text`журнал будет обрезан, если его длина превышает 2048 символов.

| **Журнале** | **Описание** |
|---|---|
| `CONNECTION` | — Инициация подключения (успешно или неуспешно); <br> — Повторное применение проверки подлинности пользователя с другим пользователем или паролем во время сеанса <br> — Завершение подключения |
| `DML_SELECT`| Запросы SELECT |
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

### <a name="connection"></a>Соединение

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (доступно только для MySQL 5,7) |
| `connection_id_d` | Уникальный идентификатор соединения, созданный MySQL |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключающегося к MySQL |
| `user_s` | Имя пользователя, который исполняет запрос |
| `db_s` | Имя базы данных, подключенной к |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="general"></a>Общие

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
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (доступно только для MySQL 5,6) |
| `event_time` | Время начала запроса в формате UTC (метка времени) |
| `error_code_d` | Код ошибки, если запрос не выполнен. `0`означает отсутствие ошибок |
| `thread_id_d` | Идентификатор потока, выполнившего запрос |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключающегося к MySQL |
| `user_s` | Имя пользователя, который исполняет запрос |
| `sql_text_s` | Текст полного запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="table-access"></a>Доступ к таблицам

> [!NOTE]
> Журналы доступа к таблицам выводятся только для MySQL 5,7.

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
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` или `DELETE` |
| `connection_id_d` | Уникальный идентификатор соединения, созданный MySQL |
| `db_s` | Имя базы данных, к которой осуществлялся доступ |
| `table_s` | Имя таблицы, к которой осуществлялся доступ |
| `sql_text_s` | Текст полного запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Анализ журналов в журналах Azure Monitor

После нахождения журналов аудита в Azure Monitor журналов с помощью журналов диагностики можно выполнить дальнейший анализ событий аудита. Ниже приведены примеры запросов, которые помогут приступить к работе. Обязательно обновите указанные ниже имена серверов.

- Вывод списка общих событий на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Вывод списка событий подключения на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Обобщение событий аудита на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Диаграмма распределение типов событий аудита на определенном сервере

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Перечисление событий аудита по всем серверам MySQL с включенными журналами диагностики для журналов аудита

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Дальнейшие шаги

- [Настройка журналов аудита в портал Azure](howto-configure-audit-logs-portal.md)