---
title: Медленные журналы запросов - База данных Azure для MariaDB
description: Описание журналов, доступных в базе данных Azure для MariaDB, и параметров для включения различных уровней ведения журналов.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272089"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Медленные журналы запросов в базе данных Azure для MariaDB
В базе данных Azure для MariaDB пользователям доступен журнал медленных запросов. Доступ к журналам транзакций не поддерживается. Журнал медленных запросов можно использовать для выявления проблем с производительностью при устранении неполадок.

Дополнительные сведения об этих журналах см. в документации к MariaDB по [журналам медленных запросов](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="configure-slow-query-logging"></a>Настройка медленной регистрации запросов
Журнал медленных запросов по умолчанию отключен. Чтобы включить его, установите `slow_query_log` НА. Это может быть включено с помощью портала Azure или Azure CLI. 

Вы можете настроить еще несколько параметров.

- **long_query_time.** Если запрос занимает больше времени, чем задано значением long_query_time (в секундах), информация о нем заносится в журнал. По умолчанию это 10 секунд.
- **log_slow_admin_statements.** Указывает, нужно ли сохранять в журнал slow_query_log административные инструкции, например ALTER_TABLE и ANALYZE_TABLE.
- **log_queries_not_using_indexes**. Указывает, нужно ли сохранять в журнал slow_query_log запросы, не использующие индексы.
- **log_throttle_queries_not_using_indexes.** Ограничивает число не использующих индексы запросов, сохраняемых в журнале медленных запросов. Этот параметр применяется, только если log_queries_not_using_indexes имеет значение "ON" (Включено).
- **log_output:** если "Файл", позволяет медленно журнал запросов быть записаны как для локального хранения сервера и Azure Монитор Диагностические журналы. Если "Нет", медленный журнал запросов будет запихниваться только в журналы диагностики мониторинга мониторинга Azure Monitor. 

> [!IMPORTANT]
> Если таблицы не индексируются, настройка `log_queries_not_using_indexes` и `log_throttle_queries_not_using_indexes` параметры ON могут повлиять на производительность MariaDB, так как все запросы, запущенные в отношении этих неиндексированных таблиц, будут записаны в журнал медленного запроса.<br><br>
> Если вы планируете регистрировать медленные запросы в течение длительного периода времени, рекомендуется установить `log_output` "Нет". Если они установлены в "File", эти журналы записываются в локальное хранилище серверов и могут повлиять на производительность MariaDB. 

Полное описание параметров журнала медленных запросов см. в [соответствующей документации к MariaDB](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Доступ к медленным журналам запросов
В базе данных Azure для MariaDB существует два варианта доступа к медленным журналам запросов: локальное хранилище серверов или диагностические журналы Azure Monitor. Это устанавливается `log_output` с использованием параметра.

Для локального хранилища серверов можно перечислить и загрузить журналы медленных запросов с помощью портала Azure или Azure CLI. На портале Azure перейдите на сервер на портале Azure. В разделе **Мониторинг** найдите страницу **Журналы сервера**. Описание работы с Azure CLI см. в статье [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-cli.md). 

Диагностические журналы Azure Monitor позволяют регистрировать медленные журналы запросов в журналы мониторинга Azure (Log Analytics), Azure Storage или Концентраторы событий. Более подробная информация приведена [ниже.](concepts-server-logs.md#diagnostic-logs)

## <a name="local-server-storage-log-retention"></a>Локальное хранение хранилища серверов
При входе в локальное хранилище сервера журналы доступны в течение семи дней с момента их создания. Если общий объем доступных журналов превышает 7 ГБ, по мере необходимости удаляются самые старые файлы.

Новый файл журнала создается каждые 24 часа или при достижении размера файла 7 ГБ.

> [!Note]
> Вышеупомянутое удержание журнала не распространяется на журналы, которые подаются по трубам с помощью диагностических журналов Azure Monitor. Можно изменить период хранения данных для испускаемых данных (например. Лазурное хранилище).

## <a name="diagnostic-logs"></a>Журналы диагностики
База данных Azure для MariaDB интегрирована с диагностическими журналами Azure Monitor. После включения журналов медленных запросов на сервере MariaDB можно выпустить их в журналы Azure Monitor, концентраторы событий или хранилище Azure. Дополнительные сведения о том, как включить журналы диагностики, см. в статье [Сбор и использование данных журнала из ресурсов Azure](../azure-monitor/platform/platform-logs-overview.md).

В приведенной ниже таблице описывается содержимое каждого журнала. Порядок появления выбранных полей зависит от выбранного метода вывода.

| **Свойство** | **Описание** |
|---|---|
| `TenantId` | Идентификатор клиента |
| `SourceSystem` | `Azure` |
| `TimeGenerated`(UTC) | Метка времени, когда журнал был записан в формате UTC |
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
| `start_time_t`(UTC) | Время начала запроса. |
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

> [!Note]
> Для, `sql_text`журнал будет усечен, если он превышает 2048 символов.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Анализ журналов в журналах Azure Monitor

После того, как журналы медленных запросов будут переданы в журналы Azure Monitor через диагностические журналы, можно выполнить дальнейший анализ ваших медленных запросов. Ниже приведены некоторые примеры запросов, которые помогут вам начать работу. Убедитесь в том, чтобы обновить ниже с вашим именем сервера.

- Запросы продолжительностью более 10 секунд на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Перечислите 5 самых длинных запросов на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Обобщение медленных запросов на минимальное, максимальное, среднее и стандартное время запроса отклонения на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- График медленного распределения запросов на определенном сервере

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Отображение запросов продолжительного более 10 секунд на всех серверах MariaDB с включенным диагностическим журналом

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Next Steps
- [Как настроить медленные журналы запросов с портала Azure](howto-configure-server-logs-portal.md)
- [Как настроить медленные журналы запросов из ClI Azure](howto-configure-server-logs-cli.md)
