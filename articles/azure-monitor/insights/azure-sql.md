---
title: Решение служб анализа SQL Azure в Azure Monitor | Документация Майкрософт
description: Как решение "Аналитика SQL Azure" поможет вам управлять базами данных SQL Azure
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 03f330f429be583d3a400eb1ee00875f7c305e74
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64923017"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Мониторинг базы данных SQL Azure с помощью решения "Аналитика SQL Azure" (предварительная версия)

![Символ службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-symbol.png)

Служба "Аналитика SQL Azure" — это расширенное облачное решение для мониторинга производительности баз данных SQL Azure, эластичных пулов и управляемых экземпляров в нужном масштабе и в нескольких подписках на одной панели. Эта служба собирает и отображает важные метрики производительности Базы данных SQL Azure благодаря встроенным средствам аналитики, которые помогают устранять соответствующие неполадки.

На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения. Решение также поможет вам определить проблемы на каждом уровне стека приложений. Метрики диагностики Azure вместе с Azure Monitor представлений используется для представления данных о всех Azure SQL баз данных, эластичных пулов и баз данных в управляемых экземплярах в одной рабочей области Log Analytics. Azure Monitor позволяет собирать, сопоставлять и визуализировать структурированные и неструктурированные данные.

Практические сведения об использовании решения "Аналитика SQL Azure" и типичные сценарии его применения приведены в видео:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Подключенные источники

Аналитика SQL Azure — это облачное решение для мониторинга с поддержкой потоковой передачи диагностических данных телеметрии для баз данных SQL Azure: отдельных, в пуле и для баз данных Управляемых экземпляров. Как решение не использует агенты для подключения к Azure Monitor, решение не поддерживает наблюдение за SQL Server, размещенный в локальной или на виртуальных машинах, ознакомьтесь со следующей таблицей совместимости.

| Подключенный источник | Поддерживаются | ОПИСАНИЕ |
| --- | --- | --- |
| [Система диагностики Azure](../platform/collect-azure-metrics-logs.md) | **Да** | Данные метрик и журнала Azure отправляются к журналам монитора Azure непосредственно из Azure. |
| [Учетная запись хранения Azure](../platform/collect-azure-metrics-logs.md) | Нет  | Azure Monitor не считывает данные из учетной записи хранения. |
| [Агенты Windows](../platform/agent-windows.md) | Нет  | Решение не использует прямые агенты Windows. |
| [Агенты Linux](../learn/quick-collect-linux-computer.md) | Нет  | Решение не использует прямые агенты Linux. |
| [Группа управления System Center Operations Manager](../platform/om-agents.md) | Нет  | Решение не использует прямое подключение из агента Operations Manager к Azure Monitor. |

## <a name="configuration"></a>Параметр Configuration
Использовать процесс, описанный в [решения добавьте Azure Monitor из коллекции решений](../../azure-monitor/insights/solutions.md) добавить решение для анализа SQL Azure (Предварительная версия) в рабочую область Log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Настройка баз данных SQL Azure, эластичных пулов и управляемых экземпляров для потоковой передачи телеметрии службы диагностики

Создав решение "Аналитика SQL Azure" в рабочей области, вам необходимо **настроить каждый** ресурс, который нужно отслеживать, для потоковой передачи его телеметрии системы диагностики в решение. Выполните подробные инструкции на этой странице:

- Включите систему диагностики Azure для базы данных SQL Azure для [потоковой передачи телеметрии системы диагностики в решение "Аналитика SQL Azure"](../../sql-database/sql-database-metrics-diag-logging.md).

Приведенная выше страница также содержит инструкции по включению поддержки для наблюдения за несколькими подписками Azure из одной рабочей области службы "Аналитика SQL Azure", выступающей в качестве единой панели.

## <a name="using-the-solution"></a>Использование решения

Вместе с решением в рабочую область добавляется элемент служб анализа SQL Azure. Кроме того, он появляется в общих сведениях. Щелкните ссылку Просмотр сводки, чтобы загрузить содержимое плитки.

![Плитка сводки службы анализа SQL Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

После загрузки на плитке отображаются количество баз данных, эластичных пулов, управляемые экземпляры и базы данных Azure SQL в управляемых экземпляров, которые получает данные телеметрии диагностики из решения.

![Элемент служб анализа SQL Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

Это решение включает два отдельных представления — одно для мониторинга баз данных SQL Azure и эластичных пулов, а другое для мониторинга управляемого экземпляра и баз данных в управляемых экземплярах.

Чтобы просмотреть панель мониторинга решения "Аналитика SQL Azure" для баз данных SQL Azure и эластичных пулов, щелкните в верхней части плитки. Чтобы просмотреть панель мониторинга решения "Аналитика SQL Azure" для управляемого экземпляра и баз данных в управляемом экземпляре, щелкните в нижней части плитки.

### <a name="viewing-azure-sql-analytics-data"></a>Просмотр данных службы "Аналитика SQL Azure"

На панели мониторинга отображаются общие сведения обо всех базах данных, мониторинг которых осуществляется через различные перспективы. Для работы различных перспектив необходимо включить соответствующие метрики или журналы на ресурсах SQL для потоковой передачи в рабочую область Log Analytics.

Обратите внимание, что если некоторые метрики или журналы не будут отправлены в Azure Monitor, плитки в решении, не заполняются данных мониторинга.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Представление базы данных SQL Azure и эластичных пулов

После выбора плитки "Аналитика SQL Azure" для базы данных отобразится панель мониторинга.

![Обзор службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-overview.png)

При выборе любой плитки открывается подробный отчет о конкретной перспективе. Выбрав перспективу, вы увидите подробный отчет.

![Время ожидания службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-metrics.png)

Каждая перспектива в этом представлении включает сводки по подписке, серверу, эластичному пулу и уровню базы данных. Кроме того, справа в каждой перспективе отображается отчет о ней. Выбор подписки, сервера, пула или базы данных из списка позволяет продолжить подробное изучение.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Управляемый экземпляр и базы данных в представлении управляемого экземпляра

После выбора плитки "Аналитика SQL Azure" для базы данных отобразится панель мониторинга.

![Обзор службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-overview-mi.png)

При выборе любой плитки открывается подробный отчет о конкретной перспективе. Выбрав перспективу, вы увидите подробный отчет.

При выборе представления управляемого экземпляра отображаются сведения об использовании управляемого экземпляра, содержащихся в нем базах данных, а также телеметрия по запросам, выполняемым в пределах экземпляра.

![Время ожидания службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Перспективы

В таблице ниже представлены перспективы, поддерживаемые для двух версий панели мониторинга — одной для базы данных SQL Azure и эластичных пулов, а другой для управляемого экземпляра.

| Перспектива | ОПИСАНИЕ | Поддержка базы данных SQL и эластичных пулов | Поддержка Управляемого экземпляра |
| --- | ------- | ----- | ----- |
| Resource by type (Ресурсы по типу) | Перспектива, в которой представлено число всех отслеживаемых ресурсов. | Yes | Yes |
| Аналитика | Предоставляет подробные сведения о производительности Intelligent Insights в иерархическом виде. | Yes | Yes |
| Errors | Предоставляет подробные данные об ошибках SQL, возникших в базах данных, в иерархическом виде. | Yes | Yes |
| Время ожидания | Предоставляет подробные данные о времени ожидания SQL в базах данных в иерархическом виде. | Yes | Нет  |
| Blockings (Блокировки) | Предоставляет подробные данные о блокировках SQL в базах данных в иерархическом виде. | Yes | Нет  |
| Database waits (Время ожидания базы данных) | Предоставляет подробную статистику времени ожидания SQL на уровне базы данных в иерархическом виде. Включает сводку по общему времени ожидания и времени ожидания для каждого типа ожидания. |Yes | Yes |
| Query duration (Длительность запросов) | Предоставляет подробную статистику о выполнении запросов, такую как продолжительность запроса, загрузку ЦП, число операций ввода-вывода данных, число операций ввода-вывода журнала, в иерархическом виде. | Yes | Yes |
| Query waits (Время ожидания запроса) | Предоставляет подробную статистику времени ожидании запросов по категории ожидания в иерархическом виде. | Yes | Yes |

### <a name="intelligent-insights-report"></a>Отчет Intelligent Insights

[Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) для базы данных SQL Azure позволяет узнать о производительности всех баз данных SQL Azure. Все собранные данные Intelligent Insights можно визуализировать в перспективе Intelligent Insights, а также получить к ним доступ.

![Аналитические сведения службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Отчеты об эластичных пулах и базах данных

Для эластичных пулов и баз данных SQL предусмотрены собственные определенные отчеты, в которых можно просмотреть все данные, собранные для ресурса в указанное время.

![База данных службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-database.png)

![Эластичный пул SQL Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Отчеты о запросах

С помощью перспективы Query duration (Длительность запроса) и Query waits (Время ожидания запроса) можно сопоставить производительность любого запроса в отчете о запросах. В этом отчете сравнивается производительность запросов в различных базах данных. Кроме того, он упрощает точное определение баз данных с высокой и низкой производительностью запросов.

![Запросы службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Разрешения

Для использования службы "Аналитика SQL Azure" пользователям нужно предоставить минимальное разрешение ​​роли читателя в Azure. Однако эта роль не позволяет им видеть текст запроса или выполнять какие-либо действия по автоматической настройке. Расширенные роли Azure, которые позволяют использовать решение в полной мере: владелец, участник, участник базы данных SQL или участник SQL Server. Вы также можете создать на портале пользовательскую роль с определенными разрешениями, необходимыми только для использования службы "Аналитика SQL Azure", и без доступа к управлению другими ресурсами.

### <a name="creating-a-custom-role-in-portal"></a>Создание пользовательской роли на портале

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Некоторые организации применяют строгие механизмы контроля разрешений в Azure, поэтому мы предоставляем следующий скрипт PowerShell, который позволяет создать на портале Azure пользовательскую роль "Оператор мониторинга Аналитики SQL" с минимальными разрешениями на чтение и запись, необходимыми для использования службы "Аналитика SQL Azure" в полной мере.

Замените {SubscriptionId} в приведенном ниже скрипте идентификатором подписки Azure и выполните скрипт, войдя в систему от имени роли владельца или участника в Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

После создания роли назначьте ее каждому пользователю, которому необходимо предоставить пользовательские разрешения на использование службы "Аналитика SQL Azure".

## <a name="analyze-data-and-create-alerts"></a>Анализ данных и создание оповещений

Анализ данных в службе "Аналитика SQL Azure" основан на [языке Log Analytics](../log-query/get-started-queries.md), который используется для пользовательских запросов и отчетов. Найдите описание доступных данных, полученных из ресурса базы данных, для отправки пользовательских запросов к [доступным метрикам и журналам](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Для настройки автоматических оповещений в решении нужно написать запрос Log Analytics, который активирует оповещение при выполнении условия. Ниже приведены несколько примеров запросов Log Analytics, по которым можно настроить оповещение в решении.

### <a name="creating-alerts-for-azure-sql-database"></a>Создание оповещений для базы данных SQL Azure

[Оповещения можно легко создать](../platform/alerts-metric.md) с помощью данных, поступающих из ресурсов службы "База данных SQL Azure". Вот несколько полезных [запросов журналов](../log-query/log-query-overview.md), которые можно использовать с оповещениями журналов:

#### <a name="high-cpu-on-azure-sql-database"></a>Высокая загрузка ЦП в Базе данных SQL Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Предварительное требование для настройки этого предупреждения заключается в том, чтобы отслеживаемые базы данных потоком передавали метрики диагностики (вариант "Все метрики") в решение.
> - Замените значение MetricName cpu_percent на dtu_consumption_percent, чтобы получить высокие результаты DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Высокая загрузка ЦП в эластичных пулах Базы данных SQL Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Предварительное требование для настройки этого предупреждения заключается в том, чтобы отслеживаемые базы данных потоком передавали метрики диагностики (вариант "Все метрики") в решение.
> - Замените значение MetricName cpu_percent на dtu_consumption_percent, чтобы получить высокие результаты DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Хранилище Базы данных SQL Azure в среднем было заполнено на 95 % в течение последнего часа

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - Предварительное требование для настройки этого предупреждения заключается в том, чтобы отслеживаемые базы данных потоком передавали метрики диагностики (вариант "Все метрики") в решение.
> - Для этого запроса требуется такая настройка правила генерации оповещений, чтобы оповещение запускалось при наличии результатов (> 0) запроса, обозначающих, что условие существует в некоторых базах данных. В выходных данных содержится список ресурсов базы данных, которые превышают порог storage_threshold в рамках определенного time_range.
> - В выходных данных содержится список ресурсов базы данных, которые превышают порог storage_threshold в рамках определенного time_range.

#### <a name="alert-on-intelligent-insights"></a>Оповещение об Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - Предварительное требование для настройки этого предупреждения заключается в том, чтобы отслеживаемые базы данных потоком передавали журнал диагностики SQLInsights в решение.
> - Для этого запроса также требуется настроить правило генерации оповещений, которое будет выполняться с той же частотой, что и alert_run_interval, чтобы избежать повторяющихся результатов. Правило должно быть настроено на то, чтобы запускать оповещение, когда для запроса существуют результаты (> 0).
> - Настройте alert_run_interval, чтобы задать диапазон времени для проверки того, произошло ли условие в базах данных, настроенных для потоковой отправки журнала SQLInsights в решение.
> - Настройте insights_string, чтобы записывать текст анализа первопричин, представленный в экземпляре аналитических сведений. Этот же текст отображается в пользовательском интерфейсе решения, который можно использовать из существующих экземпляров аналитических сведений. Кроме того, приведенный ниже запрос можно использовать для просмотра текста всех аналитических сведений, созданных в подписке. В выходных данных запроса содержатся различные строки, которые можно использовать для настройки оповещений в Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Создание оповещений для управляемого экземпляра

#### <a name="managed-instance-storage-is-above-90"></a>Хранилище Управляемых экземпляров заполнено более чем на 90 %

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Предварительным требованием для настройки этого оповещения является включение потоковой передачи журнала ResourceUsageStats Управляемого экземпляра, подлежащего мониторингу, в решении.
> - Для этого запроса требуется такая настройка правила генерации оповещений, чтобы оповещение запускалось при наличии результатов (> 0) запроса, обозначающих, что условие существует в управляемом экземпляре. Результатом является уровень загруженности хранилища (в процентах) в управляемом экземпляре.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Среднее потребление ЦП Управляемого экземпляра выше 95 % в течение последнего часа

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Предварительным требованием для настройки этого оповещения является включение потоковой передачи журнала ResourceUsageStats Управляемого экземпляра, подлежащего мониторингу, в решении.
> - Для этого запроса требуется такая настройка правила генерации оповещений, чтобы оповещение запускалось при наличии результатов (> 0) запроса, обозначающих, что условие существует в управляемом экземпляре. Выходные данные — это средний процент потребления ЦП в Управляемом экземпляре в определенный период.

### <a name="pricing"></a>Цены

Хотя за использование решения плата не взимается, учитывается использование диагностических данных телеметрии свыше бесплатных единиц приема данных, выделяемых каждый месяц. Дополнительные сведения см. [на странице цен на Log Analytics](https://azure.microsoft.com/pricing/details/monitor). Бесплатные единицы приема данных позволяют осуществлять бесплатный мониторинг нескольких баз данных каждый месяц. Обратите внимание, что более активные базы данных с более интенсивными рабочими нагрузками принимают больше данных, чем простаивающие. Вы можете легко отслеживать использование приема данных в решении, выбрав рабочую область OMS в меню навигации службы "Аналитика SQL Azure", а затем выбрав "Использование и ожидаемые затраты".

## <a name="next-steps"></a>Дальнейшие действия

- Используйте [журнал запросов](../log-query/log-query-overview.md) в Azure Monitor для просмотра подробных данных Azure SQL.
- [Создавайте пользовательские панели мониторинга](../learn/tutorial-logs-dashboards.md), отображающие данные SQL Azure.
- [Создавайте оповещения](../platform/alerts-overview.md), предупреждающие о возникновении определенных событий SQL Azure.
