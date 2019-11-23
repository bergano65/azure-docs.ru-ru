---
title: Метрики и журналы диагностики
description: Learn how to enable diagnostics in Azure SQL Database to store information about resource utilization and query execution statistics.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/15/2019
ms.openlocfilehash: 27810f2ee1bc95c924003cd8a5944860df40db14
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420804"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Метрики и журналы диагностики Базы данных SQL Azure

In this topic, you will learn how to configure logging of diagnostics telemetry for Azure SQL Database through the Azure portal, PowerShell, Azure CLI, Azure Monitor REST API, and Azure Resource Manager template. These diagnostics can be used to gauge resource utilization and query execution statistics.

Отдельные базы данных, базы данных в эластичных пулах, базы данных экземпляра в управляемом экземпляре могут передавать потоки метрик и журналов диагностики для упрощения мониторинга производительности. Вы можете настроить базу данных для передачи сведений об использовании ресурсов, о рабочих ролях и сеансах, а также настроить подключение к одному из следующих ресурсов Azure:

- **Аналитика SQL Azure:** для интеллектуального мониторинга баз данных SQL Azure, который включает в себя отчеты о производительности, оповещения и рекомендации по устранению рисков.
- **Центры событий Azure:** для интеграции данных телеметрии Базы данных SQL с настраиваемыми решениями для мониторинга или горячими конвейерами.
- **Служба хранилища Azure:** для архивации больших объемов данных телеметрии по сниженной цене.

    ![Архитектура](./media/sql-database-metrics-diag-logging/architecture.png)

Дополнительные сведения о метриках и категориях журналов, поддерживаемых различными службами Azure, см. в следующих статьях:

- [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Обзор журналов диагностики Azure](../azure-monitor/platform/resource-logs-overview.md)

В этой статье приведены рекомендации, которые помогут включить диагностическую телеметрию для баз данных SQL Azure, эластичных пулов и управляемых экземпляров. Из статьи вы узнаете, как настроить Аналитику SQL Azure в качестве инструмента мониторинга для просмотра диагностических данных телеметрии базы данных.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Включение ведения журнала диагностических данных телеметрии

Можно включить ведение таких журналов метрик и диагностической телеметрии и управлять ими, используя один из следующих методов:

- портала Azure
- PowerShell
- Azure CLI
- REST API Azure Monitor
- Шаблон Azure Resource Manager

When you enable metrics and diagnostics logging, you need to specify the Azure resource destination for collecting the diagnostics telemetry. Доступны следующие варианты:

- Аналитика SQL Azure
- Центры событий Azure
- Служба хранилища Azure

Вы можете подготовить новый ресурс Azure или выбрать имеющийся. После выбора ресурса с помощью настройки **Параметры диагностики** укажите, какие данные следует собирать.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Supported diagnostic logging for Azure SQL databases, and instance databases

Включите ведение журналов метрик и диагностики в базе данных SQL. Они не включены по умолчанию.

You can set up Azure SQL databases, and instance databases to collect the following diagnostics telemetry:

| Мониторинг телеметрии для баз данных | Поддержка отдельной базы данных и базы данных в пуле | Instance database support |
| :------------------- | ----- | ----- |
| [Basic metrics](#basic-metrics): Contains DTU/CPU percentage, DTU/CPU limit, physical data read percentage, log write percentage, Successful/Failed/Blocked by firewall connections, sessions percentage, workers percentage, storage, storage percentage, and XTP storage percentage. | ДА | Нет |
| [Instance and App Advanced](#advanced-metrics):  Contains tempdb system database data and log file size and tempdb percent log file used. | ДА | Нет |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contains information about the query runtime statistics such as CPU usage and query duration statistics. | ДА | ДА |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contains information about the query wait statistics (what your queries waited on) such are CPU, LOG, and LOCKING. | ДА | ДА |
| [Errors](#errors-dataset): Contains information about SQL errors on a database. | ДА | ДА |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset) содержит статистику по значениям времени ожидания различных типов для базы данных. | ДА | Нет |
| [Timeouts](#time-outs-dataset): Contains information about timeouts on a database. | ДА | Нет |
| [Blocks](#blockings-dataset): Contains information about blocking events on a database. | ДА | Нет |
| [Deadlocks](#deadlocks-dataset): Contains information about deadlock events on a database. | ДА | Нет |
| [AutomaticTuning](#automatic-tuning-dataset): Contains information about automatic tuning recommendations for a database. | ДА | Нет |
| [SQLInsights](#intelligent-insights-dataset): Contains Intelligent Insights into performance for a database. Дополнительные сведения см. в статье об [Intelligent Insights](sql-database-intelligent-insights.md). | ДА | ДА |

> [!IMPORTANT]
> Elastic pools and managed instances have their own separate diagnostics telemetry from databases they contain. This is important to note as diagnostics telemetry is configured separately for each of these resources, as documented below.

> [!NOTE]
> Security Audit and SQLSecurityAuditEvents logs can't be enabled from the database diagnostics settings (although showing on the screen). To enable audit log streaming, see [Set up auditing for your database](sql-database-auditing.md#subheading-2), and [auditing logs in Azure Monitor logs and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>портала Azure

You can use the **Diagnostics settings** menu for each single, pooled, or instance database in Azure portal to configure streaming of diagnostics telemetry. In addition, diagnostic telemetry can also be configured separately for database containers: elastic pools and managed instances. You can set the following destinations to stream the diagnostics telemetry: Azure Storage, Azure Event Hubs, and Azure Monitor logs.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Настройка потоковой передачи диагностических данных телеметрии для эластичных пулов

   ![Значок эластичного пула](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Вы можете настроить ресурс эластичного пула для сбора следующих диагностических данных телеметрии:

| Ресурс | Мониторинг телеметрии |
| :------------------- | ------------------- |
| **Эластичный пул** | [Basic metrics](sql-database-metrics-diag-logging.md#basic-metrics) contains eDTU/CPU percentage, eDTU/CPU limit, physical data read percentage, log write percentage, sessions percentage, workers percentage, storage, storage percentage, storage limit, and XTP storage percentage. |

To configure streaming of diagnostics telemetry for elastic pools and databases in elastic pools, you will need to separately configure **both** of the following:

- Enable streaming of diagnostics telemetry for an elastic pool, **and**
- Enable streaming of diagnostics telemetry for each database in elastic pool

This is because elastic pool is a database container with its own telemetry being separate from an individual database telemetry.

Чтобы включить потоковую передачу диагностических данных телеметрии для ресурса эластичного пула, выполните следующие действия:

1. Go to the **elastic pool** resource in Azure portal.
1. Выберите **Параметры диагностики**.
1. Выберите **Включить диагностику**, если предыдущие параметры отсутствуют, или **Настройка параметра**, чтобы изменить предыдущий параметр.

   ![Включение диагностики для эластичных пулов](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Введите имя настройки для вашей собственной ссылки.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For log analytics, select **Configure** and create a new workspace by selecting **+Create New Workspace**, or select an existing workspace.
1. Select the check box for elastic pool diagnostics telemetry: **Basic** metrics.
   ![Configure diagnostics for elastic pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

1. Щелкните **Сохранить**.
1. In addition, configure streaming of diagnostics telemetry for each database within the elastic pool you want to monitor by following steps described in the next section.

> [!IMPORTANT]
> In addition to configuring diagnostics telemetry for an elastic pool, you also need to configure diagnostics telemetry for each database in elastic pool, as documented below.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configure streaming of diagnostics telemetry for single database, or database in elastic pool

   ![Значок Базы данных SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

To enable streaming of diagnostics telemetry for single or pooled databases, follow these steps:

1. Go to Azure **SQL database** resource.
1. Выберите **Параметры диагностики**.
1. Выберите **Включить диагностику**, если предыдущие параметры отсутствуют, или **Настройка параметра**, чтобы изменить предыдущий параметр.
   - Можно создать до трех параллельных соединений для потоковой передачи диагностических данных телеметрии.
   - Select **Add diagnostic setting** to configure parallel streaming of diagnostics data to multiple resources.

   ![Включение диагностики для отдельных баз данных, баз данных в пуле или баз данных экземпляров](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

1. Введите имя настройки для вашей собственной ссылки.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For the standard, event-based monitoring experience, select the following check boxes for database diagnostics log telemetry: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**, and **Deadlocks**.
1. For an advanced, one-minute-based monitoring experience, select the check box for **Basic** metrics.
   ![Configure diagnostics for single, pooled, or instance databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Щелкните **Сохранить**.
1. Repeat these steps for each database you want to monitor.

> [!NOTE]
> Security Audit and SQLSecurityAuditEvents logs can't be enabled from the database diagnostics settings (although shown on the screen). To enable audit log streaming, see [Set up auditing for your database](sql-database-auditing.md#subheading-2), and [auditing logs in Azure Monitor logs and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

> [!TIP]
> Повторите эти шаги для каждой базы данных SQL Azure, которую вы хотите отслеживать.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Настройка потоковой передачи диагностических данных телеметрии для управляемого экземпляра

   ![Значок управляемого экземпляра](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Можно настроить ресурс управляемого экземпляра для сбора следующей диагностической телеметрии:

| Ресурс | Мониторинг телеметрии |
| :------------------- | ------------------- |
| **Управляемый экземпляр** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) содержит количество виртуальных ядер, средний процент использования ЦП, а также сведения о запросах ввода-вывода, прочитанных и записанных байтах, зарезервированном и используемом дисковом пространстве. |

To configure streaming of diagnostics telemetry for managed instance and instance databases, you will need to separately configure **both** of the following:

- Enable streaming of diagnostics telemetry for managed instance, **and**
- Enable streaming of diagnostics telemetry for each instance database

This is because managed instance is a database container with its own telemetry, separate from an individual instance database telemetry.

Чтобы включить потоковую передачу диагностических данных телеметрии для ресурса управляемого экземпляра, сделайте следующее:

1. Go to the **managed instance** resource in Azure portal.
1. Выберите **Параметры диагностики**.
1. Выберите **Включить диагностику**, если предыдущие параметры отсутствуют, или **Настройка параметра**, чтобы изменить предыдущий параметр.

   ![Включение диагностики для управляемого экземпляра](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Введите имя настройки для вашей собственной ссылки.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For log analytics, select **Configure** and create a new workspace by selecting **+Create New Workspace**, or use an existing workspace.
1. Select the check box for instance diagnostics telemetry: **ResourceUsageStats**.

   ![Настройка диагностики для управляемого экземпляра](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

1. Щелкните **Сохранить**.
1. In addition, configure streaming of diagnostics telemetry for each instance database within the managed instance you want to monitor by following the steps described in the next section.

> [!IMPORTANT]
> In addition to configuring diagnostics telemetry for a managed instance, you also need to configure diagnostics telemetry for each instance database, as documented below.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configure streaming of diagnostics telemetry for instance databases

   ![Значок базы данных экземпляра в управляемом экземпляре](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

To enable streaming of diagnostics telemetry for instance databases, follow these steps:

1. Go to **instance database** resource within managed instance.
1. Выберите **Параметры диагностики**.
1. Выберите **Включить диагностику**, если предыдущие параметры отсутствуют, или **Настройка параметра**, чтобы изменить предыдущий параметр.
   - Вы можете создать до трех (3) параллельных соединений для потоковой передачи диагностических данных телеметрии.
   - Выберите **+Add diagnostic setting** (+ Добавить параметр диагностики), чтобы настроить параллельную потоковую передачу данных диагностики на несколько ресурсов.

   ![Включение диагностики для баз данных экземпляров](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Введите имя настройки для вашей собственной ссылки.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. Select the check boxes for database diagnostics telemetry: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** and **Errors**.
   ![Configure diagnostics for instance databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Щелкните **Сохранить**.
1. Repeat these steps for each instance database you want to monitor.

> [!TIP]
> Repeat these steps for each instance database you want to monitor.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Включить ведение журналов метрик и диагностики можно с помощью PowerShell.

- Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Идентификатор учетной записи хранения представляет собой идентификатор ресурса целевой учетной записи хранения.

- Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Идентификатор правила служебной шины Azure — это строка в формате:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Идентификатор ресурса рабочей области Log Analytics можно получить с помощью следующей команды:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="to-configure-multiple-azure-resources"></a>Настройка нескольких ресурсов Azure

Чтобы обеспечить поддержку нескольких подписок, используйте сценарий PowerShell. Дополнительные сведения см. в статье [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/) (Включение ведения журнала метрик ресурсов Azure с помощью PowerShell).

Укажите идентификатор ресурса рабочей области \<$WSID\> в качестве параметра при выполнении скрипта `Enable-AzureRMDiagnostics.ps1` для отправки данных диагностики из нескольких ресурсов в рабочую область.

- Чтобы получить идентификатор рабочей области \<$WSID\> места назначения для ваших диагностических данных, используйте следующий сценарий:

    ```powershell
    $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Замените \<subID\> идентификатором подписки, \<RG_NAME\> именем группы ресурсов, а \<WS_NAME\> — именем рабочей области.

### <a name="azure-cli"></a>Azure CLI

Можно включить ведение журналов метрик и диагностики с помощью Azure CLI.

> [!NOTE]
> Scripts to enable diagnostics logging are supported for Azure CLI v1.0. Please note that CLI v2.0 is unsupported at this time.

- Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Идентификатор учетной записи хранения представляет собой идентификатор ресурса целевой учетной записи хранения.

- Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Идентификатор правила служебной шины — это строка в формате:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="rest-api"></a>REST API

Сведения об изменении параметров диагностики с помощью REST API Azure Monitor см. в [этом документе](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Шаблон Resource Manager

Сведения о включении параметров диагностики при создании ресурса из шаблона Resource Manager см. [здесь](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Потоковая передача данных в службу Аналитика SQL Azure

Аналитика SQL Azure — это облачное решение, которое отслеживает производительность баз данных SQL Azure, эластичных пулов и управляемого экземпляра в нужном масштабе и в нескольких подписках. Решение поможет собирать и визуализировать показатели производительности Базы данных SQL Azure, а также обладает встроенными интеллектуальными возможностями для устранения неполадок производительности.

![Обзор службы "Аналитика SQL Azure"](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Журналы метрик и диагностики Базы данных SQL можно передать в службу "Аналитика SQL Azure" с помощью встроенной функции **Отправить в Log Analytics** на вкладке настроек диагностики на портале. You can also enable log analytics by using a diagnostics setting via PowerShell cmdlets, the Azure CLI, or the Azure Monitor REST API.

### <a name="installation-overview"></a>Обзор установки

Можно контролировать парк Баз данных SQL Azure с помощью службы "Аналитика SQL Azure". Выполните следующие действия:

1. Создайте решение "Аналитика SQL Azure" из Azure Marketplace.
2. Создайте рабочую область мониторинга в решении.
3. Настройте базы данных для потоковой передачи диагностических данных телеметрии в рабочую область.

Если вы используете эластичные пулы или управляемые экземпляры, вам также необходимо настроить потоковую передачу диагностических данных телеметрии из этих ресурсов.

### <a name="create-azure-sql-analytics-resource"></a>Создание ресурса Аналитика SQL Azure

1. Выполните поиск по запросу "Аналитика SQL Azure" в Azure Marketplace и выберите найденное решение.

   ![Поиск службы Аналитика SQL Azure на портале](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Выберите **Создать** на экране обзора решения.

3. В форме "Аналитика SQL Azure" введите необходимые дополнительные сведения: имя рабочей области, подписку, группу ресурсов, расположение и ценовую категорию.

   ![Настройка службы "Аналитика SQL Azure" на портале](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Нажмите **ОК** для подтверждения, а затем выберите **Создать**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Настройка баз данных для записи метрик и журналов диагностики

The easiest way to configure where databases record metrics is by using the Azure portal. Как описано выше, перейдите к ресурсу Базы данных SQL на портале Azure и выберите **Параметры диагностики**.

Если вы используете эластичные пулы или управляемый экземпляр, необходимо также настроить параметры диагностики в этих ресурсах, чтобы диагностические данные телеметрии могли передаваться в рабочую область.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Use the SQL Analytics solution for monitoring and alerting

Аналитику SQL можно использовать в качестве иерархической панели мониторинга для просмотра ресурсов Базы данных SQL.

- Дополнительные сведения об использовании решения "Аналитика SQL" см. в статье [Мониторинг Базы данных SQL Azure с помощью служб анализа SQL Azure (предварительная версия) в Log Analytics](../log-analytics/log-analytics-azure-sql.md).
- To learn how to setup alerts for SQL Database and managed instance based on SQL Analytics, see [Creating alerts for SQL Database and managed instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Потоковая передача в Центры событий

Можно передавать потоки метрик и журналов диагностики Базы данных SQL в концентраторы событий с помощью встроенного параметра **Передать в концентратор событий** на портале Azure. Можно также включить идентификатор правила служебной шины с помощью параметра диагностики через командлеты PowerShell, Azure CLI или Azure Monitor REST API.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Что делать с журналами метрик и диагностики в Центрах событий

Выполнив потоковую передачу выбранных данных в Центры событий, вы становитесь на один шаг ближе к включению дополнительных сценариев мониторинга. Центры событий выступают в качестве "двери" для конвейера событий. После сбора данных в концентраторе событий их можно преобразовывать и сохранять с помощью поставщика аналитики в реальном времени или адаптера хранения. Центры событий отделяют производство потока событий от потребления этих событий. Таким образом потребители событий могут получать доступ к событиям по собственному расписанию. Дополнительные сведения о Центрах событий см. в следующих статьях:

- [Что такое Центры событий?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Начало работы с Центрами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Потоковые метрики в Центрах событий можно использовать для следующих целей:

- **View service health by streaming hot-path data to Power BI**

   С помощью Центров событий, Stream Analytics и Power BI можно в близком к реальному времени получать аналитическую информацию о службах Azure на основе метрик и диагностических данных. Обзор настройки концентраторов событий, обработки данных в Stream Analytics и вывода информации через Power BI см. в статье [Stream Analytics и Power BI. Панель мониторинга для анализа потоковой передачи данных](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream logs to third-party logging and telemetry streams**

   С помощью потоковой передачи Центров событий вы можете передать журналы метрик и диагностики в различные решения мониторинга и решения для анализа журналов.

- **Build a custom telemetry and logging platform**

   У вас уже есть платформа телеметрии, созданная на заказ, или вы планируете ее создать? В Центрах событий реализованы возможности масштабирования, публикации и подписки. Решение обеспечивает гибкие функции приема журналов диагностики. Ознакомьтесь с [руководством Дэна Росановы (Dan Rosanova) по использованию Центров событий для глобальной платформы телеметрии](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Потоковая передача в хранилище

Журналы метрик и диагностики Базы данных SQL можно хранить в службе хранилища Azure с помощью встроенной возможности **Архивировать в учетной записи хранения** на портале Azure. Хранилище также можно включить, воспользовавшись параметром диагностики с помощью командлетов PowerShell, Azure CLI или REST API Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Схема журналов метрик и диагностики в учетной записи хранения

После настройки сбора метрик и журналов диагностики в выбранной учетной записи хранения создается контейнер хранилища, как только первые строки данных станут доступными. Вот как выглядит структура большого двоичного объекта:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Или даже еще проще:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

For example, a blob name for Basic metrics might be:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Имя BLOB-объекта для хранения данных из эластичного пула выглядит следующим образом:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Политика хранения данных и цены

Если выбрать Центры событий или учетную запись хранения, можно указать политику хранения. Эта политика удаляет данные, которые хранятся дольше выбранного периода времени. При указании Log Analytics политика хранения определяется на основании выбранной ценовой категории. В этом случае предоставляемые бесплатные единицы приема данных позволяют выполнять бесплатный мониторинг нескольких баз данных каждый месяц. Любое потребление диагностических данных телеметрии в объеме свыше предоставляемых бесплатно единиц, может повлечь за собой затраты. Помните, что активные базы данных с более интенсивными рабочими нагрузками принимают больше данных, чем неактивные. For more information, see [Log analytics pricing](https://azure.microsoft.com/pricing/details/monitor/).

Если вы используете Аналитику SQL Azure, то можете отслеживать использование приема данных в решении, выбрав элемент **Рабочая область OMS** в меню навигации Аналитики SQL Azure, а затем выбрав **Потребление** и **Estimated Costs** (Оценка затрат).

## <a name="metrics-and-logs-available"></a>Доступные метрики и журналы

Monitoring telemetry available for Azure SQL Database, elastic pools and managed instance is documented below. Collected monitoring telemetry inside SQL Analytics can be used for your own custom analysis and application development using [Azure Monitor log queries](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) language.

## <a name="basic-metrics"></a>Basic metrics

Refer to the following tables for details about Basic metrics by resource.

> [!NOTE]
> Basic metrics option was formerly known as All metrics. The change made was to the naming only and there was no change to the metrics monitored. This change was initiated to allow for introduction of additional metric categories in the future.

### <a name="basic-metrics-for-elastic-pools"></a>Basic metrics for elastic pools

|**Ресурс**|**Метрики**|
|---|---|
|Эластичный пул|Сведения о проценте использования DTU, используемых единицах DTU, ограничении DTU, проценте использования ЦП, проценте чтения физических данных, проценте записей в журнал, проценте сеансов, проценте рабочих ролей, хранилище, проценте хранилища, ограничении хранилища, проценте хранилища XTP. |

### <a name="basic-metrics-for-azure-sql-databases"></a>Basic metrics for Azure SQL Databases

|**Ресурс**|**Метрики**|
|---|---|
|База данных SQL Azure|Сведения о проценте использования DTU, используемых единицах DTU, ограничении DTU, проценте использования ЦП, проценте чтения физических данных, проценте записей в журнал, проценте успешных, неудачных или заблокированных подключений брандмауэра, проценте сеансов, проценте рабочих ролей, хранилище, проценте хранилища, проценте хранилища XTP и взаимоблокировках. |

## <a name="advanced-metrics"></a>Advanced metrics

Refer to the following table for details about advanced metrics.

|**Метрика**|**Отображаемое имя метрики**|**Описание**|
|---|---|---|
|tempdb_data_size| Tempdb Data File Size Kilobytes |Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |
|tempdb_log_size| Tempdb Log File Size Kilobytes |Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |
|tempdb_log_used_percent| Tempdb Percent Log Used |Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |

## <a name="basic-logs"></a>Basic logs

Details of telemetry available for all logs are documented in the tables below. Please see [supported diagnostic logging](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) to understand which logs are supported for a particular database flavor - Azure SQL single, pooled, or instance database.

### <a name="resource-usage-stats-for-managed-instance"></a>Resource usage stats for managed instance

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure.|
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: ResourceUsageStats |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: MANAGEDINSTANCES |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя управляемого экземпляра |
|ResourceId|Универсальный код ресурса (URI) |
|SKU_s|Номер SKU продукта управляемого экземпляра |
|virtual_core_count_s|Доступное количество виртуальных ядер |
|avg_cpu_percent_s|Средний процент использования ЦП |
|reserved_storage_mb_s|Зарезервированная емкость хранилища в управляемом экземпляре |
|storage_space_used_mb_s|Использованный объем хранилища в управляемом экземпляре |
|io_requests_s|Количество операций ввода-вывода |
|io_bytes_read_s|Количество считанных байтов операций ввода-вывода |
|io_bytes_written_s|Количество записанных байтов операций ввода-вывода |

### <a name="query-store-runtime-statistics"></a>Статистика среды выполнения хранилища запросов

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: QueryStoreRuntimeStatistics. |
|OperationName|Имя операции. Всегда: QueryStoreRuntimeStatisticsEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|query_hash_s|Хэш запроса |
|query_plan_hash_s|Хэш плана запроса. |
|statement_sql_handle_s|Дескриптор SQL инструкции. |
|interval_start_time_d|Начальное значение datetimeoffset интервала в тактах с 01.01.1900 |
|interval_end_time_d|Конечное значение datetimeoffset интервала в тактах с 01.01.1900 |
|logical_io_writes_d|Общее число логических операций записи ввода-вывода |
|max_logical_io_writes_d|Максимальное число логических операций записи ввода-вывода при выполнении каждого запроса |
|physical_io_reads_d|Общее число физических операций чтения ввода-вывода |
|max_physical_io_reads_d|Максимальное число логических операций чтения ввода-вывода при выполнении каждого запроса |
|logical_io_reads_d|Общее число логических операций чтения ввода-вывода |
|max_logical_io_reads_d|Максимальное число логических операций чтения ввода-вывода при выполнении каждого запроса |
|execution_type_d|Тип выполнения. |
|count_executions_d|Число выполнений запроса |
|cpu_time_d|Общее процессорное время, затраченное на выполнение запроса, в микросекундах |
|max_cpu_time_d|Максимальное потребление процессорного времени на выполнение одного запроса в микросекундах |
|dop_d|Сумма степеней параллелизма |
|max_dop_d|Максимальная степень параллелизма, используемая для выполнения одного запроса |
|rowcount_d|Общее число возвращаемых строк |
|max_rowcount_d|Максимальное число строк, возвращаемых при выполнении одного запроса |
|query_max_used_memory_d|Общий объем используемой памяти в килобайтах |
|max_query_max_used_memory_d|Максимальный объем памяти, используемой при выполнении одного запроса, в килобайтах |
|duration_d|Общее время выполнения запроса в миллисекундах |
|max_duration_d|Максимальное время выполнения одного запроса |
|num_physical_io_reads_d|Общее число физических операций чтения |
|max_num_physical_io_reads_d|Максимальное число физических операций чтения при выполнении каждого запроса |
|log_bytes_used_d|Общий объем записанного журнала в байтах |
|max_log_bytes_used_d|Максимальное число байтов журнала, используемых при выполнения каждого запроса |
|query_id_d|Идентификатор запроса в хранилище запросов |
|plan_id_d|Идентификатор плана в хранилище запросов |

[Дополнительные сведения о статистических данных среды выполнения хранилища запросов.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>Статистика времени ожидания хранилища запросов

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: QueryStoreWaitStatistics. |
|OperationName|Имя операции. Всегда: QueryStoreWaitStatisticsEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|wait_category_s|Категория времени ожидания |
|is_parameterizable_s|Указывает, подлежит ли запрос параметризации |
|statement_type_s|Тип инструкции |
|statement_key_hash_s|Хэш ключа инструкции |
|exec_type_d|Типа выполнения. |
|total_query_wait_time_ms_d|Общее время ожидания запроса в определенной категории времени ожидания |
|max_query_wait_time_ms_d|Максимальное время ожидания запроса при отдельном выполнении в определенной категории времени ожидания. |
|query_param_type_d|0 |
|query_hash_s|Хэш запроса в хранилище запросов |
|query_plan_hash_s|Хэш плана запроса в хранилище запросов. |
|statement_sql_handle_s|Дескриптор инструкции в хранилище запросов. |
|interval_start_time_d|Начальное значение datetimeoffset интервала в тактах с 01.01.1900 |
|interval_end_time_d|Конечное значение datetimeoffset интервала в тактах с 01.01.1900 |
|count_executions_d|Количество выполнений запроса. |
|query_id_d|Идентификатор запроса в хранилище запросов |
|plan_id_d|Идентификатор плана в хранилище запросов |

Дополнительные сведения о [статистических данных времени ожидания хранилища запросов](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Набор данных ошибок

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: Errors. |
|OperationName|Имя операции. Всегда: ErrorEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|Сообщение|Сообщение об ошибке в виде обычного текста. |
|user_defined_b|Указывает, установлен ли бит ошибки пользователем. |
|error_number_d|Код ошибки |
|Серьезность|Серьезность ошибки. |
|state_d|Состояние ошибки. |
|query_hash_s|Хэш запроса, завершившегося сбоем (при наличии) |
|query_plan_hash_s|Хэш плана запроса для запроса, завершившегося сбоем (при наличии) |

Дополнительные сведения о [сообщениях об ошибках SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Набор данных статистики времени ожидания базы данных

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: DatabaseWaitStatistics. |
|OperationName|Имя операции. Всегда: DatabaseWaitStatisticsEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|wait_type_s|Имя типа времени ожидания. |
|start_utc_date_t [UTC]|Время начала измеренного периода |
|end_utc_date_t [UTC]|Время окончания измеренного периода |
|delta_max_wait_time_ms_d|Максимальное время ожидания при выполнении одного запроса. |
|delta_signal_wait_time_ms_d|Общее время ожидания сигнала |
|delta_wait_time_ms_d|Общее время ожидания в течение периода. |
|delta_waiting_tasks_count_d|Число ожидающих задач. |

Дополнительные сведения о [статистике времени ожидания базы данных](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Набор данных времени ожидания

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: Timeouts. |
|OperationName|Имя операции. Всегда: TimeoutEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|error_state_d|Код состояния ошибки. |
|query_hash_s|Хэш запроса, если он доступен |
|query_plan_hash_s|Хэш плана запроса, если он доступен |

### <a name="blockings-dataset"></a>Набор данных блокировки

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: Blocks. |
|OperationName|Имя операции. Всегда: BlockEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|lock_mode_s|Режим блокировки, используемый для запроса. |
|resource_owner_type_s|Владелец блокировки |
|blocked_process_filtered_s|Отчет о заблокированных процессах в формате XML |
|duration_d|Длительность блокировки в микросекундах |

### <a name="deadlocks-dataset"></a>Набор данных взаимоблокировки

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC] |Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: Deadlocks. |
|OperationName|Имя операции. Всегда: DeadlockEvent. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|deadlock_xml_s|Отчет о взаимоблокировке в формате XML |

### <a name="automatic-tuning-dataset"></a>Набор данных автоматической настройки

|Свойство|Описание|
|---|---|
|TenantId|Идентификатор клиента |
|SourceSystem|Всегда: Azure. |
|TimeGenerated [UTC]|Метка времени, когда был записан журнал |
|Тип|Всегда: AzureDiagnostics. |
|ResourceProvider|Имя поставщика ресурсов. Всегда: MICROSOFT.SQL. |
|Категория|Имя категории. Всегда: AutomaticTuning. |
|Ресурс|Имя ресурса. |
|ResourceType|Имя типа ресурса. Всегда: SERVERS/DATABASES. |
|SubscriptionId|GUID подписки для базы данных |
|ResourceGroup|Имя группы ресурсов для базы данных |
|LogicalServerName_s|Имя сервера для базы данных |
|LogicalDatabaseName_s|Имя базы данных. |
|ElasticPoolName_s|Имя эластичного пула для базы данных (при наличии) |
|DatabaseName_s|Имя базы данных. |
|ResourceId|Универсальный код ресурса (URI) |
|RecommendationHash_s|Уникальный хэш рекомендаций по автоматической настройке |
|OptionName_s|Операция автоматической настройки |
|Schema_s|Схема базы данных |
|Table_s|Затронутая таблица |
|IndexName_s|Имя индекса |
|IndexColumns_s|Имя столбца |
|IncludedColumns_s|Включенные столбцы |
|EstimatedImpact_s|Предполагаемое влияние JSON-файла рекомендаций по автоматической настройке |
|Event_s|Тип события автоматической настройки |
|Timestamp_t|Метка времени последнего обновления |

### <a name="intelligent-insights-dataset"></a>Набор данных Intelligent Insights

Дополнительные сведения о [формате журнала Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы научиться включать ведение журнала и узнать, какие метрики и категории журналов поддерживаются различными службами Azure, ознакомьтесь со следующими статьями:

- [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Обзор журналов диагностики Azure](../azure-monitor/platform/resource-logs-overview.md)

Дополнительные сведения о Центрах событий см. в статье:

- [Что такое Центры событий Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Начало работы с Центрами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

To learn how to setup alerts based on telemetry from log analytics see:

- [Creating alerts for SQL Database and managed instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
