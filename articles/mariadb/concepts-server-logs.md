---
title: Журналы запросов с высокой производительностью — база данных Azure для MariaDB
description: Описание журналов, доступных в базе данных Azure для MariaDB, и параметров для включения различных уровней ведения журналов.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 3f82a061f276aaf786bc0a9bae15b60e8bdda009
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846341"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Журналы запросов в базе данных Azure для MariaDB
В базе данных Azure для MariaDB пользователям доступен журнал медленных запросов. Доступ к журналам транзакций не поддерживается. Журнал медленных запросов можно использовать для выявления проблем с производительностью при устранении неполадок.

Дополнительные сведения об этих журналах см. в документации к MariaDB по [журналам медленных запросов](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Доступ к журналам запросов с задержкой
Вы можете перечислить и скачать базу данных Azure для MariaDB журналов запросов с помощью портал Azure и Azure CLI.

На портале Azure выберите нужный сервер Базы данных Azure для MariaDB. В разделе **Мониторинг** найдите страницу **Журналы сервера**.

Описание работы с Azure CLI см. в статье [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-cli.md).

Аналогичным образом можно передать журналы, чтобы Azure Monitor с помощью журналов диагностики. Дополнительные сведения см. [ниже](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Хранение журналов
Журналы доступны в течение семи дней с момента создания. Если общий объем доступных журналов превышает 7 ГБ, по мере необходимости удаляются самые старые файлы.

Новый файл журнала создается каждые 24 часа или при достижении размера файла 7 ГБ.

## <a name="configure-slow-query-logging"></a>Настройка ведения журнала запросов с задержкой
Журнал медленных запросов по умолчанию отключен. Чтобы включить его, установите для параметра slow_query_log значение "ON" (Включено).

Вы можете настроить еще несколько параметров.

- **long_query_time.** Если запрос занимает больше времени, чем задано значением long_query_time (в секундах), информация о нем заносится в журнал. По умолчанию это 10 секунд.
- **log_slow_admin_statements.** Указывает, нужно ли сохранять в журнал slow_query_log административные инструкции, например ALTER_TABLE и ANALYZE_TABLE.
- **log_queries_not_using_indexes**. Указывает, нужно ли сохранять в журнал slow_query_log запросы, не использующие индексы.
- **log_throttle_queries_not_using_indexes.** Ограничивает число не использующих индексы запросов, сохраняемых в журнале медленных запросов. Этот параметр применяется, только если log_queries_not_using_indexes имеет значение "ON" (Включено).
- **log_output**. Если значение — "File", журнал запросов может быть записан как в локальное хранилище сервера, так и в Azure Monitor журналов диагностики. Если задано значение "нет", журнал запросов будет записываться только в Azure Monitor журналы диагностики. 

> [!IMPORTANT]
> Если таблицы не индексируются, установка параметров `log_queries_not_using_indexes` и `log_throttle_queries_not_using_indexes` в значение ON может повлиять на производительность MariaDB, так как все запросы к этим неиндексированным таблицам будут записываться в журнал медленных запросов.<br><br>
> Если вы планируете ведение журнала запросов в течение продолжительного периода времени, рекомендуется присвоить параметру `log_output` значение None. Если задано значение "File", эти журналы записываются в хранилище локального сервера и могут повлиять на производительность MariaDB. 

Полное описание параметров журнала медленных запросов см. в [соответствующей документации к MariaDB](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="diagnostic-logs"></a>Журналы диагностики
База данных Azure для MariaDB интегрирована с журналами диагностики Azure Monitor. Включив журналы запросов на сервере MariaDB, вы можете выбрать их для Azure Monitor журналов, концентраторов событий или службы хранилища Azure. Дополнительные сведения о том, как включить журналы диагностики, см. в статье [Сбор и использование данных журнала из ресурсов Azure](../azure-monitor/platform/platform-logs-overview.md).

В приведенной ниже таблице описывается содержимое каждого журнала. Порядок появления выбранных полей зависит от выбранного метода вывода.

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Метка времени, когда журнал был записан в формате UTC |
| `Type` | Тип журнала Всегда `AzureDiagnostics` |
| `SubscriptionId` | Идентификатор GUID для подписки, принадлежащей серверу |
| `ResourceGroup` | Имя группы ресурсов, принадлежащей серверу |
| `ResourceProvider` | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Универсальный код ресурса (URI) |
| `Resource` | Имя сервера |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Имя сервера |
| `start_time_t` [UTC] | Время начала запроса. |
| `query_time_s` | Общее время, которое потребовалось для выполнения запроса. |
| `lock_time_s` | Общее время блокировки запроса. |
| `user_host_s` | Имя пользователя |
| `rows_sent_s` | Количество отправленных строк. |
| `rows_examined_s` | Число проверенных строк. |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Вставить идентификатор |
| `sql_text_s` | Полный запрос. |
| `server_id_s` | Идентификатор сервера |
| `thread_id_s` | Идентификатор потока |
| `\_ResourceId` | Универсальный код ресурса (URI) |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Анализ журналов в журналах Azure Monitor

После передвижения журналов запросов в Azure Monitor журналов с помощью журналов диагностики можно выполнить дальнейший анализ запросов. Ниже приведены примеры запросов, которые помогут приступить к работе. Обязательно обновите указанные ниже имена серверов.

- Запросы с более чем 10 секундами на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Вывод списка 5 самых длинных запросов на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Суммирование запросов с минимальным, максимальным, средним и стандартным отклонением времени запроса на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- График распределения скорости запросов на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Отображение запросов дольше 10 секунд для всех серверов MariaDB с включенными журналами диагностики

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Следующие шаги
- [Настройка журналов запросов от портал Azure](howto-configure-server-logs-portal.md)
- [Настройка журналов запросов от Azure CLI](howto-configure-server-logs-cli.md)
