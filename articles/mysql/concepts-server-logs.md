---
title: Журналы сервера в службе "База данных Azure для MySQL"
description: Описание журналов, доступных в базе данных Azure для MySQL, и параметров для управления уровнями ведения журнала.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 10/03/2018
ms.openlocfilehash: c9f8fc4bee370f287b40275b76fa98d2552d7600
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545079"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Журналы сервера в базе данных Azure для MySQL
В базе данных Azure для MySQL пользователям доступен журнал медленных запросов. Доступ к журналам транзакций не поддерживается. Журнал медленных запросов можно использовать для выявления проблем с производительностью при устранении неполадок. 

Дополнительные сведения о журнале медленных запросов MySQL см. в [этом разделе справочного руководства по MySQL](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Доступ к журналам сервера
Чтобы просмотреть и скачать журналы сервера в базе данных Azure для MySQL, можно воспользоваться порталом Azure или Azure CLI.

На портале Azure выберите нужный сервер базы данных Azure для MySQL. В разделе **Мониторинг** найдите страницу **Журналы сервера**.

Описание работы с Azure CLI см. в статье [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Хранение журналов
Журналы доступны в течение семи дней с момента создания. Если общий объем доступных журналов превышает 7 ГБ, по мере необходимости удаляются самые старые файлы. 

Новый файл журнала создается каждые 24 часа или при достижении размера файла 7 ГБ.


## <a name="configure-logging"></a>Настройка журнала 
Журнал медленных запросов по умолчанию отключен. Чтобы включить его, установите для параметра slow_query_log значение "ON" (Включено).

Вы можете настроить еще несколько параметров.

- **long_query_time.** Если запрос занимает больше времени, чем задано значением long_query_time (в секундах), информация о нем заносится в журнал. По умолчанию это 10 секунд.
- **log_slow_admin_statements.** Указывает, нужно ли сохранять в журнал slow_query_log административные инструкции, например ALTER_TABLE и ANALYZE_TABLE.
- **log_queries_not_using_indexes**. Указывает, нужно ли сохранять в журнал slow_query_log запросы, не использующие индексы.
- **log_throttle_queries_not_using_indexes**. Ограничивает число не использующих индексы запросов, сохраняемых в журнале медленных запросов. Этот параметр применяется, только если log_queries_not_using_indexes имеет значение "ON" (Включено).

Полное описание параметров, применимых для журнала медленных запросов, вы найдете в [соответствующем разделе документации по MySQL](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="diagnostic-logs"></a>Журналы диагностики
База данных Azure для MySQL интегрирована с журналами диагностики Azure Monitor. После активации ведения журналов меленных запросов на сервере MySQL вы можете направить их в Log Analytics, Центры событий или службу хранилища Azure. Дополнительные сведения о том, как включить журналы диагностики, см. в статье [Сбор и использование данных журнала из ресурсов Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

В приведенной ниже таблице описывается содержимое каждого журнала. Порядок появления выбранных полей зависит от выбранного метода вывода.

| **Свойство** | **Описание** |
|---|---|---|
| TenantId | Идентификатор клиента |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Метка времени, когда журнал был записан в формате UTC |
| type | Тип журнала Всегда `AzureDiagnostics` |
| SubscriptionId | Идентификатор GUID для подписки, принадлежащей серверу |
| ResourceGroup | Имя группы ресурсов, принадлежащей серверу |
| ResourceProvider | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | Универсальный код ресурса (URI) |
| Ресурс | Имя сервера |
| Категория | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | Имя сервера |
| start_time_t [UTC] | Время начала запроса. |
| query_time_s | Общее время, которое потребовалось для выполнения запроса. |
| lock_time_s | Общее время блокировки запроса. |
| user_host_s | Имя пользователя |
| rows_sent_s | Количество отправленных строк. |
| rows_examined_s | Число проверенных строк. |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Идентификатор для вставки. |
| sql_text_s | Полный запрос. |
| server_id_s | Идентификатор сервера. |
| thread_id_s | Идентификатор потока. |
| \_ResourceId | Универсальный код ресурса (URI) |

## <a name="next-steps"></a>Дальнейшие действия
- [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md) (Настройка и использование журналов сервера с помощью Azure CLI)
