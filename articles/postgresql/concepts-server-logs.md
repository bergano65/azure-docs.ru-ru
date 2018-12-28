---
title: Журналы сервера в базе данных Azure для PostgreSQL
description: В этой статье объясняется, как служба "База данных Azure для PostgreSQL" создает журналы запросов и ошибок и как настраивается срок хранения журналов.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2ee9f750ff52b8afe4be54233f1374f523a789f4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845177"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Журналы сервера в базе данных Azure для PostgreSQL 
База данных Azure для PostgreSQL создает журналы запросов и ошибок. Журналы запросов и ошибок можно использовать для идентификации, устранения и исправления ошибок конфигурации, а также для повышения производительности. (Доступ к журналам транзакций не включен). 

## <a name="configure-logging"></a>Настройка журнала 
Чтобы настроить ведение журнала сервера, для него требуется задать некоторые параметры. На каждом новом сервере параметры **log_checkpoints** и **log_connections** заданы по умолчанию. Чтобы ведение журнала соответствовало вашим требования, можно изменить дополнительные параметры. 

![База данных Azure для PostgreSQL. Параметры ведения журнала](./media/concepts-server-logs/log-parameters.png)

Дополнительные сведения об этих параметрах см. в документации PostgreSQL [Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Отчеты об ошибках и ведение журнала). Дополнительные сведения о настройке параметров базы данных Azure для PostgreSQL см. в статье [Настройка параметров сервера на портале Azure](howto-configure-server-parameters-using-portal.md) или [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Доступ к журналам сервера с помощью портала или интерфейса командной строки
Если функция ведения журналов была включена, то для доступа к хранилищу журналов базы данных Azure для PostgreSQL можно использовать [портал Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md) или Azure REST API. Файлы журналов создаются каждый час или по достижении размера 100 МБ (в зависимости от того, какое событие произойдет первым). Вы можете настроить срок хранения журналов, используя параметр  **log\_retention\_period** , связанный с сервером. Значению по умолчанию соответствует три дня, а максимальному значению семь. Ваш сервер должен иметь достаточный объем выделенной памяти для хранения файлов журналов. (Данный параметр хранения не влияет на Журналы диагностики Azure).


## <a name="diagnostic-logs"></a>Журналы диагностики
База данных Azure для PostgreSQL интегрирована с журналами диагностики Azure Monitor. После активации ведения журналов на сервере PostgreSQL пользователь может выбрать отправлять их в [Log Analytics](../azure-monitor/log-query/log-query-overview.md), центры событий или в службу хранилища Azure. Дополнительные сведения о том, как включить журналы диагностики, см. в статье [Сбор и использование данных журнала из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). 


В приведенной ниже таблице описывается содержимое каждого журнала. Порядок появления выбранных полей зависит от выбранной конечной точки вывода. 

|**Поле** | **Описание** |
|---|---|
| TenantId | Идентификатор клиента |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Метка времени, когда журнал был записан в формате UTC |
| type | Тип журнала Всегда `AzureDiagnostics` |
| SubscriptionId | Идентификатор GUID для подписки, принадлежащей серверу |
| ResourceGroup | Имя группы ресурсов, принадлежащей серверу |
| ResourceProvider | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Универсальный код ресурса (URI) |
| Ресурс | Имя сервера |
| Категория | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Пример уровня ведения журнала: LOG, ERROR, NOTICE |
| Сообщение | Первичное сообщение журнала | 
| Домен | Версия сервера, например: postgres 10 |
| Описание | Второстепенное сообщение журнала (если применимо) |
| ColumnName | Имя столбца (если применимо) |
| SchemaName | Имя схемы (если применимо) |
| DatatypeName | Имя типа данных (если применимо) |
| LogicalServerName | Имя сервера | 
| _ResourceId | Универсальный код ресурса (URI) |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о доступе к журналам см. в статье [Настройка журналов сервера и получение к ним доступа с помощью портала Azure](howto-configure-server-logs-in-portal.md) или [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-using-cli.md).
- Дополнительные сведения см. в статье [Цены на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
