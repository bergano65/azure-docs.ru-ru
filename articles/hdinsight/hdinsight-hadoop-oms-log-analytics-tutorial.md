---
title: Использование журналов Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для мониторинга заданий, выполняемых в кластере HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 41688792330214943eeb116dc4b5aaf7eebfeebf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192048"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Use Azure Monitor logs to monitor HDInsight clusters (Мониторинг кластеров HDInsight с помощью журналов Azure Monitor)

Узнайте, как включить журналы Azure Monitor для отслеживания операций кластера Hadoop в HDInsight. И как добавить решение для мониторинга HDInsight.

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это служба Azure Monitor, которая наблюдает за облачными и локальными средами. Мониторинг заключается в поддержке их доступности и производительности. Она собирает данные, созданные ресурсами в облаке, в локальных средах и в других средствах мониторинга. Данные используются для обеспечения анализа в нескольких источниках.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область Log Analytics. Эту рабочую область можно рассматривать как уникальную Azure Monitor среду журналов с собственным репозиторием данных, источниками данных и решениями. С инструкциями можно ознакомиться в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Кластер Azure HDInsight. В настоящее время журналы Azure Monitor можно использовать со следующими типами кластеров HDInsight:

  * Hadoop
  * HBase
  * Интерактивный запрос
  * Kafka
  * Spark
  * Storm

  Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell AZ Module.  См. статью [Знакомство с новым модулем Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Убедитесь, что у вас установлена последняя версия. При необходимости выполните команду `Update-Module -Name Az`.

> [!NOTE]  
> Для повышения производительности рекомендуется разместить кластер HDInsight и рабочую область Log Analytics в одном регионе. Журналы Azure Monitor недоступны во всех регионах Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Включение журналов Azure Monitor с помощью портала

В этом разделе вы настроите существующий кластер HDInsight Hadoop для использования рабочей области Azure Log Analytics, чтобы отслеживать задания, журналы отладки и т. д.

1. В [портал Azure](https://portal.azure.com/)выберите свой кластер. Кластер открывается на новой странице портала.

1. Слева в разделе **мониторинг**выберите **Azure Monitor**.

1. В главном представлении в разделе **Интеграция с Azure Monitor**выберите **включить**.

1. В раскрывающемся списке **Выберите рабочую область** выберите существующую рабочую область Log Analytics.

1. Щелкните **Сохранить**.  Параметр сохраняется в течение нескольких секунд.

    ![Включение мониторинга для кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Включение мониторинга для кластеров HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Включение журналов Azure Monitor с помощью Azure PowerShell

Вы можете включить Azure Monitor журналов с помощью командлета Azure PowerShell AZ Module [Enable-аздинсигхтмониторинг](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) .

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Чтобы отключить, используйте командлет [Disable-аздинсигхтмониторинг](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Установка решений по управлению кластерами HDInsight

HDInsight предоставляет решения по управлению кластерами, которые можно добавить для Azure Monitor журналов. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) добавляют функции в журналы Azure Monitor, предоставляя дополнительные инструменты для работы с данными и анализа. Эти решения собираются важные метрики производительности из кластеров HDInsight. И предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения.

Доступные решения HDInsight:

* Мониторинг HDInsight Hadoop
* HDInsight HBase Monitoring
* мониторинг HDInsight Interactive Query;
* Мониторинг Kafka HDInsight
* Мониторинг HDInsight Spark
* Мониторинг HDInsight Storm

Инструкции по решению для управления см. [в статье решения для управления в Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Чтобы поэкспериментировать, установите решение для мониторинга Hadoop в HDInsight. По завершении вы увидите плитку **хдинсигхсадуп** в разделе Summary ( **Сводка**). Выберите элемент **HDInsightHadoop**. Решение HDInsightHadoop будет выглядеть примерно следующим образом:

![Решение HDInsight для мониторинга](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Так как это новый кластер, в отчете не будут отображаться никакие действия.

## <a name="configuring-performance-counters"></a>Настройка счетчиков производительности

Azure Monitor поддерживает сбор и анализ метрик производительности для узлов в кластере. Дополнительные сведения см. [в разделе источники данных производительности Linux в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Аудит кластера

HDInsight поддерживает аудит кластеров с помощью журналов Azure Monitor, импортируя следующие типы журналов:

* `log_gateway_audit_CL`— Эта таблица предоставляет журналы аудита из узлов шлюза кластера, которые показывают успешные и неудачные попытки входа.
* `log_auth_CL`— Эта таблица предоставляет журналы SSH с успешным и неудачным попытками входа.
* `log_ambari_audit_CL`— Эта таблица предоставляет журналы аудита из Ambari.
* `log_ranger_audti_CL`— Эта таблица предоставляет журналы аудита из Apache Ranger в кластерах ESP.

## <a name="next-steps"></a>Дальнейшие действия

* [Запрос журналов Azure Monitor для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Как отслеживать доступность кластера с помощью Apache Ambari и журналов Azure Monitor](./hdinsight-cluster-availability.md)
