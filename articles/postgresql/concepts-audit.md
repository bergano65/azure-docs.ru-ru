---
title: Регистрация аудита - База данных Azure для PostgreS'L - Единый сервер
description: Концепции для pgAudit аудита регистрации в базе данных Azure для PostgreS-L - Единый сервер.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842475"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Регистрация аудита в базе данных Azure для PostgreS-L - Единый сервер

Аудит регистрации деятельности базы данных в базе данных Azure для PostgreS-L - Единый сервер доступен через расширение аудита PostgreS'L: [pgAudit](https://www.pgaudit.org/). pgAudit обеспечивает подробную регистрацию сеансов и/или аудита объектов.

> [!NOTE]
> pgAudit находится в предварительном просмотре на базе данных Azure для PostgreS'L.
> Расширение может быть включено только на серверах общего назначения и оптимизации памяти.

Если вам нужны журналы ресурсов уровня Azure для таких операций, [Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md)как вычисление и масштабирование хранилища, см.

## <a name="usage-considerations"></a>Особенности использования
По умолчанию выписки из журналов pgAudit создаются вместе с регулярными выписками с использованием стандартного средства ведения журнала Postgres. В Базе данных Azure для PostgreSQL эти файлы журнала можно скачать на портале Azure или с помощью интерфейса командной строки. Максимальное хранилище для сбора файлов составляет 1 ГБ, и каждый файл доступен максимум семь дней (по умолчанию три дня). Эта услуга является краткосрочным вариантом хранения.

Кроме того, можно настроить все журналы, которые будут излучаться в службу диагностического журнала Azure Monitor. Если включить диагностическую систему Azure Monitor, ваши журналы будут автоматически отправлены (в формате JSON) в журналы Хранения Azure, концентраторы событий и/или журналы Azure Monitor в зависимости от вашего выбора.

Включение pgAudit приводит к созданию большого объема журналов на сервере, что влияет на производительность и хранение журналов. Мы рекомендуем использовать службу журналов диагностики Azure, которая предлагает варианты долговременного хранения, а также функции анализа и оповещений. Мы рекомендуем отключить стандартное ведение журнала, чтобы уменьшить влияние дополнительного ведения журнала на производительность:

   1. Установите `logging_collector` параметр для OFF. 
   2. Перезапустите сервер, чтобы применить это изменение.

Чтобы узнать, как настроить журналы для хранения azure, концентраторов событий или журналов Azure Monitor, посетите раздел диагностических журналов [статьи журналов сервера.](concepts-server-logs.md)

## <a name="installing-pgaudit"></a>Установка pgAudit

Чтобы установить pgAudit, необходимо включить его в общие библиотеки предварительной загрузки сервера. Изменение параметра Postgres `shared_preload_libraries` требует перезагрузки сервера, чтобы ввести в действие. Вы можете изменить параметры с помощью [портала Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)или [REST API.](/rest/api/postgresql/configurations/createorupdate)

Использование [портала Azure:](https://portal.azure.com)

   1. Выберите сервер базы данных Azure для PostgreSQL.
   2. На боковой панели выберите **параметры сервера.**
   3. Найдите параметр `shared_preload_libraries`.
   4. Выберите **pgaudit**.
   5. Перезапустите сервер для применения изменения.

   6. Подключитесь к серверу с помощью клиента (например, psql) и включите расширение pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Если вы видите ошибку, подтвердите, что вы перезапустили сервер после сохранения. `shared_preload_libraries`

## <a name="pgaudit-settings"></a>настройки pgAudit

pgAudit позволяет настроить журнал сеанса или объект аудита. [Регистрация аудита сеансов](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) испускает подробные журналы выполненных отчетов. [Регистрация аудита объектов](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) — это аудит, присваиваемый определенным отношениям. Вы можете настроить один или оба типа журналов. 

> [!NOTE]
> Настройки pgAudit определяются gloabally и не могут быть указаны на уровне базы данных или роли.

После установки [pgAudit,](#installing-pgaudit)вы можете настроить его параметры, чтобы начать журнал. [Документация pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) содержит определение каждого параметра. Сначала проверьте параметры и подтвердите, что вы получаете ожидаемое поведение.

> [!NOTE]
> Установка `pgaudit.log_client` ON перенаправит журналы в процесс клиента (например, psql), а не на написание файла. Этот параметр обычно следует отключать. <br> <br>
> `pgaudit.log_level`включентолько только `pgaudit.log_client` тогда, когда включен.

> [!NOTE]
> В базе данных Azure `pgaudit.log` для PostgreS'L нельзя установить `-` ярлык (минус), как описано в документации pgAudit. Все необходимые классы операторов (READ, WRITE и т. д.) должны быть указаны отдельно.

### <a name="audit-log-format"></a>Формат журнала аудита
Каждая запись аудита `AUDIT:` указывается в начале строки журнала. Формат остальной части записи подробно описан в [документации pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Если вам нужны другие поля, чтобы удовлетворить ваши `log_line_prefix`требования аудита, используйте параметр Postgres. `log_line_prefix`— это строка, которая выводима в начале каждой строки журнала Postgres. Например, следующий `log_line_prefix` параметр предоставляет отметку времени, имя пользователя, имя базы данных и идентификатор процесса:

```
t=%m u=%u db=%d pid=[%p]:
```

Чтобы узнать `log_line_prefix`больше о, посетите [документацию PostgreS'L](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Начало работы
Чтобы быстро приступить `pgaudit.log` `WRITE`к работе, установите на и откройте журналы для просмотра вывода. 

## <a name="viewing-audit-logs"></a>Просмотр журналов аудита
Если вы используете файлы .log, журналы аудита будут включены в тот же файл, что и журналы ошибок PostgreS'L. Вы можете скачать файлы журнала с [портала](howto-configure-server-logs-in-portal.md) Azure или [CLI.](howto-configure-server-logs-using-cli.md) 

Если вы используете диагностическую систему Azure, способ доступа к журналам зависит от того, какую конечную точку вы выберете. В статье для хранения журналов смотрите статью учетной [записи журналов.](../azure-monitor/platform/resource-logs-collect-storage.md) Для концентраторов событий смотрите статью [журналов потоков Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Для журналов мониторинга Azure журналы отправляются в выбранное рабочее пространство. В журналах Postgres используется режим сбора **AzureDiagnostics,** поэтому их можно запрашивать из таблицы AzureDiagnostics. Поля в таблице описаны ниже. Подробнее о запросе и оповещения читайте в обзоре [запросов Azure Monitor Logry.](../azure-monitor/log-query/log-query-overview.md)

Этот запрос можно использовать для начала работы. Можно настроить оповещения на основе запросов.

Поиск всех журналов Postgres для конкретного сервера в последний день
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Дальнейшие действия
- [Узнайте о регистрации в базе данных Azure для PostgreS'L](concepts-server-logs.md)
- Узнайте, как установить параметры с помощью [портала Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)или [REST API.](/rest/api/postgresql/configurations/createorupdate)
