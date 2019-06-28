---
title: Журналы аудита для базы данных Azure для MySQL
description: Описание журналов аудита, доступных в базе данных Azure для MySQL и параметры, доступные для включения уровней ведения журнала.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a82afe6f5299609fd6dd57a54f04f49fad5d2268
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357645"
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
| `TimeGenerated` [UTC] | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (доступно только для MySQL 5.7) |
| `connection_id` | Уникальный идентификатор соединения созданные MySQL |
| `host` | Пустой |
| `ip` | IP-адрес клиента при подключении к MySQL |
| `user` | Имя пользователя, выполняющего запрос |
| `db` | Имя базы данных, подключенных к |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="general"></a>Общие сведения

Следующая схема применяется к Общие, DML_SELECT, DML_NONSELECT, DML, DDL, ODC-ФАЙЛ и АДМИНИСТРАТОРА типов событий.

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (доступно только для версии MySQL 5.6) |
| `event_time` | Запрос запуска секунд в метку времени UNIX |
| `error_code` | Код ошибки, если не удалось выполнить запрос. `0` Ошибка не означает, что |
| `thread_id` | Идентификатор потока, который выполняет запрос |
| `host` | Пустой |
| `ip` | IP-адрес клиента при подключении к MySQL |
| `user` | Имя пользователя, выполняющего запрос |
| `sql_text` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

### <a name="table-access"></a>Доступ к таблице

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, или `DELETE` |
| `connection_id` | Уникальный идентификатор соединения созданные MySQL |
| `db` | Имя базы данных |
| `table` | Имя таблицы, доступ к |
| `sql_text` | Полный текст запроса |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="next-steps"></a>Дальнейшие действия

- [Как настроить журналы аудита на портале Azure](howto-configure-audit-logs-portal.md)