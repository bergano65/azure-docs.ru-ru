---
title: Решение azure S'L Analytics в Azure Monitor (англ.) Документы Майкрософт
description: Как решение "Аналитика SQL Azure" поможет вам управлять базами данных SQL Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275468"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Мониторинг базы данных SQL Azure с помощью решения "Аналитика SQL Azure" (предварительная версия)

![Символ службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-symbol.png)

Azure S'L Analytics — это передовое решение для мониторинга производительности всех баз данных Azure S'L в масштабе и по нескольким подпискам в одном представлении. Аналитика Azure s'L Analytics собирает и визуализирует ключевые показатели производительности со встроенным интеллектом для устранения неполадок.

Используя эти собранные метрики, можно создать пользовательские правила мониторинга и оповещения. Аналитика Azure S'L Analytics помогает выявлять проблемы на каждом уровне стека приложений. Он использует метрики Azure Diagnostic вместе с представлениями Azure Monitor для представления данных обо всех базах данных Azure S'L в одном рабочем пространстве Log Analytics. Azure Monitor помогает собирать, соотносить и визуализировать структурированные и неструктурированные данные.

Практические сведения об использовании решения "Аналитика SQL Azure" и типичные сценарии его применения приведены в видео:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Подключенные источники

Azure S'L Analytics — это облачное решение для мониторинга, поддерживающее потоковую передачу телеметрии диагностики для всех баз данных Azure S'L. Поскольку Azure S'L Analytics не использует агенты для подключения к Azure Monitor, она не поддерживает мониторинг s-L Server, размещенных на территории или в виртуальных машинах.

| Подключенный источник | Поддерживается | Описание |
| --- | --- | --- |
| [Настройки диагностики](../platform/diagnostic-settings.md) | **Да** | Метрические данные Azure и данные журналов отправляются в журналы Azure Monitor непосредственно Azure. |
| [Учетная запись хранения Azure](../platform/collect-azure-metrics-logs.md) | нет | Azure Monitor не считывает данные из учетной записи хранения. |
| [Агенты Windows](../platform/agent-windows.md) | нет | Прямые агенты Windows не используются аналитикой Azure S'L. |
| [Агенты Linux](../learn/quick-collect-linux-computer.md) | нет | Прямые агенты Linux не используются аналитикой Azure S'L. |
| [Группа управления System Center Operations Manager](../platform/om-agents.md) | нет | Прямая связь агента управления операциями с Azure Monitor не используется аналитикой Azure S'L. |

## <a name="azure-sql-analytics-options"></a>Параметры аналитики Azure S'L

В приведенной ниже таблице изложены поддерживаемые варианты для двух версий панели мониторинга Azure S'L Analytics: одной для отдельных и объединенных баз данных и эластичных пулов, а другой — для управляемых экземпляров и баз данных экземпляров.

| Опция аналитики Azure S'L | Описание | Поддержка единой и объединенной базы данных и эластичных пулов | Поддержка базы данных управляемых экземпляров и экземпляров |
| --- | ------- | ----- | ----- |
| Resource by type (Ресурсы по типу) | Перспектива, в которой представлено число всех отслеживаемых ресурсов. | Да | Да |
| Аналитика | Предоставляет подробные сведения о производительности Intelligent Insights в иерархическом виде. | Да | Да |
| ошибки | Предоставляет подробные данные об ошибках SQL, возникших в базах данных, в иерархическом виде. | Да | Да |
| Время ожидания | Предоставляет подробные данные о времени ожидания SQL в базах данных в иерархическом виде. | Да | нет |
| Blockings (Блокировки) | Предоставляет подробные данные о блокировках SQL в базах данных в иерархическом виде. | Да | нет |
| Database waits (Время ожидания базы данных) | Предоставляет подробную статистику времени ожидания SQL на уровне базы данных в иерархическом виде. Включает сводку по общему времени ожидания и времени ожидания для каждого типа ожидания. |Да | нет |
| Query duration (Длительность запросов) | Предоставляет подробную статистику о выполнении запросов, такую как продолжительность запроса, загрузку ЦП, число операций ввода-вывода данных, число операций ввода-вывода журнала, в иерархическом виде. | Да | Да |
| Query waits (Время ожидания запроса) | Предоставляет подробную статистику времени ожидании запросов по категории ожидания в иерархическом виде. | Да | Да |

## <a name="configuration"></a>Параметр Configuration

Используйте процесс, описанный в [решениях Add Azure Monitor из галереи Решений,](../../azure-monitor/insights/solutions.md) чтобы добавить аналитику Azure S'L Analytics (Preview) в рабочее пространство log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Настройка баз данных Azure S-L для потоковой передачи телеметрии диагностики

После того как вы создали решение Azure S'L Analytics в рабочей области, необходимо **настроить каждый** ресурс, который вы хотите контролировать, чтобы передавать телеметрию диагностики в аналитику Azure S'L. Выполните подробные инструкции на этой странице:

- Включите систему диагностики Azure для базы данных SQL Azure для [потоковой передачи телеметрии системы диагностики в решение "Аналитика SQL Azure"](../../sql-database/sql-database-metrics-diag-logging.md).

Приведенная выше страница также содержит инструкции по включению поддержки для наблюдения за несколькими подписками Azure из одной рабочей области службы "Аналитика SQL Azure", выступающей в качестве единой панели.

## <a name="using-azure-sql-analytics"></a>Использование аналитики Azure S'L

При добавлении аналитики Azure S'L Analytics в рабочее пространство в рабочее пространство добавляется плитка Azure S'L Analytics и отображается в Обзоре. Выберите ссылку «Свежоедело представление» для загрузки содержимого плитки.

![Сводка «Аналитика» Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

После загрузки плитка показывает количество единых и объединенных баз данных, эластичных пулов, управляемых экземпляров и управляемых баз данных экземпляров, из которых Azure S'L Analytics получает диагностическую телеметрию.

![Элемент служб анализа SQL Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure S'L Analytics предоставляет два отдельных представления: один для мониторинга отдельных баз данных и объединенных баз данных и эластичных пулов, а другой — для мониторинга управляемых экземпляров и баз данных экземпляров.

Чтобы просмотреть панель мониторинга мониторинга Azure S'L Analytics для отдельных и объединенных баз данных и эластичных пулов, нажмите на верхнюю часть плитки. Чтобы просмотреть панель мониторинга мониторинга Azure S'L Analytics для управляемых экземпляров и баз данных экземпляров, нажмите на нижнюю часть плитки.

### <a name="viewing-azure-sql-analytics-data"></a>Просмотр данных службы "Аналитика SQL Azure"

На панели мониторинга отображаются общие сведения обо всех базах данных, мониторинг которых осуществляется через различные перспективы. Для работы различных точек зрения необходимо включить надлежащие метрики или журналы на ресурсах S'L, которые будут передаваться в рабочее пространство Log Analytics.

Если некоторые метрики или журналы не передаются в Azure Monitor, плитки в Azure S'L Analytics не заполнены информацией о мониторинге.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Единые и объединенные базы данных и представление эластичных пулов

После выбора плитки "Аналитика SQL Azure" для базы данных отобразится панель мониторинга.

![Обзор службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-overview.png)

При выборе любой плитки открывается подробный отчет о конкретной перспективе. Выбрав перспективу, вы увидите подробный отчет.

![Время ожидания службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-metrics.png)

Каждая перспектива в этом представлении содержит резюме на уровнях подписки, сервера, эластичного пула и базы данных. Кроме того, справа в каждой перспективе отображается отчет о ней. Выбор подписки, сервера, пула или базы данных из списка позволяет продолжить подробное изучение.

### <a name="managed-instance-and-instances-databases-view"></a>Представление управляемых экземпляров и экземпляров баз данных

После выбора плитки "Аналитика SQL Azure" для базы данных отобразится панель мониторинга.

![Обзор службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-overview-mi.png)

При выборе любой плитки открывается подробный отчет о конкретной перспективе. Выбрав перспективу, вы увидите подробный отчет.

Выбор управляемого представления экземпляра показывает сведения об использовании управляемого экземпляра, базах данных, которые он содержит, и телеметрии по запросам, выполняемым в экземпляре.

![Время ожидания службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Отчет Intelligent Insights

[Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) для Базы данных SQL Azure позволяет узнать о производительности всех баз данных SQL Azure. Все собранные данные Intelligent Insights можно визуализировать в перспективе Intelligent Insights, а также получить к ним доступ.

![Аналитические сведения службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Упругие пулы и отчеты о базах данных

Как эластичные пулы, так и базы данных имеют свои собственные конкретные отчеты, которые отображают все данные, собранные для ресурса в указанное время.

![База данных службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-database.png)

![Эластичный пул SQL Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Отчеты о запросах

В перспективе запроса и ожидания запроса можно соотнести производительность любого запроса через отчет запроса. В этом отчете сравнивается производительность запросов в различных базах данных. Кроме того, он упрощает точное определение баз данных с высокой и низкой производительностью запросов.

![Запросы службы "Аналитика SQL Azure"](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Разрешения

Для использования службы "Аналитика SQL Azure" пользователям нужно предоставить минимальное разрешение ​​роли читателя в Azure. Однако эта роль не позволяет им видеть текст запроса или выполнять какие-либо действия по автоматической настройке. Более разрешительные роли в Azure, позволяющие в полной мере использовать аналитику Azure S'L, — это Владелец, Автор, СЗЛ ДБ или серверный вклад. Вы также можете создать на портале пользовательскую роль с определенными разрешениями, необходимыми только для использования службы "Аналитика SQL Azure", и без доступа к управлению другими ресурсами.

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

Автоматизированное оповещение в Azure S'L Analytics основано на написании запроса Log Analytics, который запускает оповещение о состоянии. Ниже приведены несколько примеров запросов log Analytics, на которых можно настроить оповещение в Azure S'L Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Создание оповещений для Базы данных SQL Azure

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
>
> - Предварительное требование создания этого оповещения состоит в том, что контролируемые базы данных передают основные метрики в Azure S'L Analytics.
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
>
> - Предварительное требование создания этого оповещения состоит в том, что контролируемые базы данных передают основные метрики в аналитику Azure S'L Analytics.
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
>
> - Предварительное требование создания этого оповещения состоит в том, что контролируемые базы данных передают основные метрики в Azure S'L Analytics.
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
>
> - Предварительное требование о настройке этого оповещения заключается в том, что контролируемые базы данных передают журнал диагностики s'LInsights в azure S'L Analytics.
> - Для этого запроса также требуется настроить правило генерации оповещений, которое будет выполняться с той же частотой, что и alert_run_interval, чтобы избежать повторяющихся результатов. Правило должно быть настроено на то, чтобы запускать оповещение, когда для запроса существуют результаты (> 0).
> - Настройте alert_run_interval, чтобы указать временной диапазон, чтобы проверить, произошло ли условие в базах данных, настроенных для потоковой передачи журнала S'LInsights в Azure S'L Analytics.
> - Настройте insights_string, чтобы записывать текст анализа первопричин, представленный в экземпляре аналитических сведений. Это тот же текст, отображаемый в ui аналитики Azure S'L, который можно использовать из существующих аналитических данных. Кроме того, приведенный ниже запрос можно использовать для просмотра текста всех аналитических сведений, созданных в подписке. В выходных данных запроса содержатся различные строки, которые можно использовать для настройки оповещений в Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Создание предупреждений для управляемых экземпляров

#### <a name="managed-instance-storage-is-above-90"></a>Управляемое хранилище экземпляров превышает 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Предварительное требование настройки этого оповещения состоит в том, что мониторинг управляемого экземпляра имеет потоковую передачу журнала ResourceUsageStats в azure S'L Analytics.
> - Этот запрос требует настройки правила оповещения для отключаемого оповещения при наличии результатов (> 0 результатов) из запроса, означая, что условие существует в управляемом экземпляре. Выход — это потребление процентного объема хранилища в управляемом экземпляре.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Управляемый экземпляр CPU среднее потребление выше 95% в течение последних 1 часа

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Предварительное требование настройки этого предупреждения состоит в том, что в отслеживаемом управляемом экземпляре потоковая передача журнала ResourceUsageStats включена в azure S'L Analytics.
> - Этот запрос требует настройки правила оповещения для отключаемого оповещения при наличии результатов (> 0 результатов) из запроса, означая, что условие существует в управляемом экземпляре. Выход — это среднее процентное потребление использования процессора в определенном периоде в управляемом экземпляре.

### <a name="pricing"></a>Цены

В то время как аналитика Azure S'L Analytics является бесплатной в использовании, потребление диагностической телеметрии выше свободных единиц данных, выделяемых каждый месяц, [см.](https://azure.microsoft.com/pricing/details/monitor) Бесплатные единицы приема данных позволяют осуществлять бесплатный мониторинг нескольких баз данных каждый месяц. Более активные базы данных с более тяжелыми рабочими нагрузками глотают больше данных по сравнению с простаивающими базами данных. Вы можете легко контролировать потребление данных в Azure S'L Analytics, выбирая рабочее пространство OMS в меню навигации аналитики Azure s'L, а затем выбирая расходы на использование и смету.

## <a name="next-steps"></a>Дальнейшие действия

- Используйте [запросы журналов](../log-query/log-query-overview.md) в Azure Monitor для просмотра подробных данных Azure S'L.
- [Создавайте пользовательские панели мониторинга](../learn/tutorial-logs-dashboards.md), отображающие данные SQL Azure.
- [Создавайте оповещения](../platform/alerts-overview.md), предупреждающие о возникновении определенных событий SQL Azure.
