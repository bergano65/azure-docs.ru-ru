---
title: Журналы сервера в базе данных Azure для PostgreSQL — один сервер
description: В этой статье описывается, как база данных Azure для PostgreSQL-Single Server создает журналы запросов и ошибок, а затем определяет срок хранения журнала.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091120"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Журналы сервера в базе данных Azure для PostgreSQL — один сервер
База данных Azure для PostgreSQL создает журналы запросов и ошибок. Журналы запросов и ошибок можно использовать для идентификации, устранения и исправления ошибок конфигурации, а также для повышения производительности. (Доступ к журналам транзакций не включен). 

## <a name="configure-logging"></a>Настройка журнала 
Чтобы настроить ведение журнала сервера, для него требуется задать некоторые параметры. На каждом новом сервере параметры **log_checkpoints** и **log_connections** заданы по умолчанию. Чтобы ведение журнала соответствовало вашим требования, можно изменить дополнительные параметры. 

![База данных Azure для PostgreSQL. Параметры ведения журнала](./media/concepts-server-logs/log-parameters.png)

Дополнительные сведения об этих параметрах см. в документации PostgreSQL [Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Отчеты об ошибках и ведение журнала). Дополнительные сведения о настройке параметров базы данных Azure для PostgreSQL см. в статье [Настройка параметров сервера на портале Azure](howto-configure-server-parameters-using-portal.md) или [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Доступ к журналам сервера с помощью портала или интерфейса командной строки
Если функция ведения журналов была включена, то для доступа к хранилищу журналов базы данных Azure для PostgreSQL можно использовать [портал Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md) или Azure REST API. Файлы журналов создаются каждый час или по достижении размера 100 МБ (в зависимости от того, какое событие произойдет первым). Вы можете настроить срок хранения журналов, используя параметр  **log\_retention\_period** , связанный с сервером. Значению по умолчанию соответствует три дня, а максимальному значению семь. Ваш сервер должен иметь достаточный объем выделенной памяти для хранения файлов журналов. (Данный параметр хранения не влияет на Журналы диагностики Azure).


## <a name="diagnostic-logs"></a>Журналы диагностики
База данных Azure для PostgreSQL интегрирована с журналами диагностики Azure Monitor. Включив журналы на сервере PostgreSQL, вы можете включить их в [журналы Azure Monitor](../azure-monitor/log-query/log-query-overview.md), концентраторы событий или службу хранилища Azure. 

> [!IMPORTANT]
> Эта диагностическая функция для журналов сервера доступна только в общего назначения и оптимизированных для памяти [ценовых категориях](concepts-pricing-tiers.md).

Чтобы включить журналы диагностики с помощью портал Azure:

   1. На портале выберите *параметры диагностики* в меню навигации сервера postgres.
   2. Выберите *Добавить параметр диагностики*.
   3. Назовите этот параметр. 
   4. Выберите предпочтительное нисходящие расположение (учетная запись хранения, концентратор событий, log Analytics). 
   5. Выберите нужные типы данных.
   6. Сохраните вашу настройку.

В следующей таблице описывается, что содержится в каждом журнале. Порядок появления выбранных полей зависит от выбранной конечной точки вывода. 

|**Поле** | **Описание** |
|---|---|
| TenantId | Идентификатор клиента |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Метка времени, когда журнал был записан в формате UTC |
| Type | Тип журнала Всегда `AzureDiagnostics` |
| SubscriptionId | Идентификатор GUID для подписки, принадлежащей серверу |
| ResourceGroup | Имя группы ресурсов, принадлежащей серверу |
| ResourceProvider | Имя поставщика ресурсов. Всегда `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Универсальный код ресурса (URI) |
| Resource | Имя сервера |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Пример уровня ведения журнала: LOG, ERROR, NOTICE |
| Сообщение | Первичное сообщение журнала | 
| Домен | Версия сервера, например: postgres 10 |
| Подробности | Второстепенное сообщение журнала (если применимо) |
| ColumnName | Имя столбца (если применимо) |
| SchemaName | Имя схемы (если применимо) |
| DatatypeName | Имя типа данных (если применимо) |
| LogicalServerName | Имя сервера | 
| _ResourceId | Универсальный код ресурса (URI) |
| Prefix | Префикс строки журнала |



## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о доступе к журналам см. в статье [Настройка журналов сервера и получение к ним доступа с помощью портала Azure](howto-configure-server-logs-in-portal.md) или [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-using-cli.md).
- Дополнительные сведения см. в статье [Цены на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
