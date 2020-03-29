---
title: Аудит журналы - База данных Azure для MyS'L
description: Описывает журналы аудита, доступные в базе данных Azure для MyS'L, а также доступные параметры для включения уровней регистрации.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062545"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Аудит журналов в базе данных Azure для MyS'L

В базе данных Azure для MyS'L журнал аудита доступен пользователям. Журнал аудита может использоваться для отслеживания действий на уровне базы данных и обычно используется для обеспечения соответствия требованиям.

> [!IMPORTANT]
> Функциональность журнала аудита в настоящее время находится в предварительном просмотре.

## <a name="configure-audit-logging"></a>Настройка журналов аудита

По умолчанию журнал аудита отключен. Чтобы включить его, установите `audit_log_enabled` НА.

Вы можете настроить еще несколько параметров.

- `audit_log_events`: контролирует регистрированные события. Ниже приведена таблица для конкретных событий аудита.
- `audit_log_include_users`: Пользователи MyS'L будут включены для ведения журнала. Значение по умолчанию для этого параметра пусто, которое будет включать в себя всех пользователей для регистрации. Это имеет более `audit_log_exclude_users`высокий приоритет над . Максимальная длина параметра составляет 512 символов.
> [!Note]
> `audit_log_include_users`имеет более `audit_log_exclude_users`высокий приоритет над . Например, `audit_log_include_users`  =  `demouser` если `audit_log_exclude_users`  =  `demouser`пользователь будет включен в журналы `audit_log_include_users` аудита, потому что имеет более высокий приоритет.
- `audit_log_exclude_users`: Пользователи MyS'L будут исключены из журнала. Максимальная длина параметра составляет 512 символов.

> [!Note]
> Для, `sql_text`журнал будет усечен, если он превышает 2048 символов.

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
| `TABLE_ACCESS` | - Доступно только для MyS'L 5.7 <br> - Таблица читать заявления, такие как SELECT или INSERT INTO ... Выберите <br> - Выписки по удалению таблицы, такие как DELETE или TRUNCATE TABLE <br> - Операторы вставки таблицы, такие как INSERT или REPLACE <br> - Заявления об обновлении таблицы, такие как ОБНОВЛЕНИЕ |

## <a name="access-audit-logs"></a>Доступ к журналам аудита

Аудиторские журналы интегрированы с диагностическими журналами Azure Monitor. После включения журналов аудита на сервере MyS'L можно изложить их в журналы Azure Monitor, концентраторы событий или хранилище Azure. Подробнее о том, как включить диагностические журналы, можно узнать на портале Azure, смотрите [статью портала журналов аудита](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Схемы диагностических журналов

В следующих разделах описывается, что выводится журналами аудита MyS'L в зависимости от типа события. Порядок появления выбранных полей зависит от выбранного метода вывода.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` , (доступно только для MyS'L 5.7) |
| `connection_id_d` | Уникальный идентификатор подключения, генерируемый MyS'L |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключаемого к MyS'L |
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
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Имя сервера |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` , (доступно только для MyS'L 5.6) |
| `event_time` | Время начала запроса в метке времени UTC |
| `error_code_d` | Код ошибки, если запрос не удался. `0`означает отсутствие ошибки |
| `thread_id_d` | Идентификатор потока, выполняемый запросом |
| `host_s` | Пусто |
| `ip_s` | IP-адрес клиента, подключаемого к MyS'L |
| `user_s` | Имя пользователя, выдающего запрос |
| `sql_text_s` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="table-access"></a>Доступ к таблицам

> [!NOTE]
> Журналы доступа к таблицам вывешиваются только для MyS'L 5.7.

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
| `connection_id_d` | Уникальный идентификатор подключения, генерируемый MyS'L |
| `db_s` | Имя базы данных, доступ к ней |
| `table_s` | Имя таблицы, доступ к ней |
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

- Список проверенных событий на всех серверах MyS'L с включенными диагностическими журналами для журналов аудита

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Дальнейшие действия

- [Как настроить журналы аудита на портале Azure](howto-configure-audit-logs-portal.md)