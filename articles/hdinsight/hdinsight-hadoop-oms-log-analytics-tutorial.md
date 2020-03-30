---
title: Используйте журналы Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для мониторинга заданий, работающих в кластере HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162792"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Use Azure Monitor logs to monitor HDInsight clusters (Мониторинг кластеров HDInsight с помощью журналов Azure Monitor)

Узнайте, как включить журналы Azure Monitor в мониторинг операций кластера Hadoop в HDInsight и как добавить решение мониторинга HDInsight.

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это служба в Azure Monitor, которая отслеживает облачные и предприимчивые среды для поддержания их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Если у вас нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/) перед началом.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область Log Analytics. Это рабочее пространство можно считать уникальной средой журналов Azure Monitor с собственным репозиторием данных, источниками данных и решениями. С инструкциями можно ознакомиться в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Кластер Azure HDInsight. В настоящее время можно использовать журналы Azure Monitor со следующими типами кластеров HDInsight:

  * Hadoop
  * HBase
  * Интерактивный запрос
  * Kafka
  * Spark
  * Storm

  Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Модуль Azure PowerShell Az.  Представляем [новый модуль Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) Убедитесь, что у вас есть последняя версия. При необходимости `Update-Module -Name Az`бегите.

> [!NOTE]  
> Для повышения производительности рекомендуется разместить кластер HDInsight и рабочую область Log Analytics в одном регионе. Журналы Azure Monitor доступны не во всех регионах Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Включить журналы Azure Monitor с помощью портала

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. На [портале Azure](https://portal.azure.com/)выберите кластер.  Инструкции см. в разделе [Отображение кластеров](./hdinsight-administer-use-portal-linux.md#showClusters). Кластер открывается на новой странице портала.

1. Слева, под **мониторингом,** выберите **Azure Monitor.**

1. С основной точки зрения, под **интеграцией Azure Monitor,** выберите **Enable**.

1. В раскрывающемся списке **Выберите рабочую область** выберите существующую рабочую область Log Analytics.

1. Нажмите кнопку **Сохранить**.  Параметр сохраняется в течение нескольких секунд.

    ![Включить мониторинг для кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Включить мониторинг для кластеров HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Включить журналы Azure Monitor с помощью Azure PowerShell

Вы можете включить журналы Azure Monitor с помощью модуля Azure PowerShell Az [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet.

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

Чтобы отключить, используйте [Смдлет Отработка-AzHDInsightMonitoring:](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Установка решений по управлению кластерами HDInsight

HDInsight предоставляет решения для управления кластерными группами, которые можно добавить для журналов Azure Monitor. [Решения для управления](../log-analytics/log-analytics-add-solutions.md) добавляют функциональность в журналы Azure Monitor, предоставляя дополнительные инструменты для данных и анализа. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения.

Вот доступные решения HDInsight:

* Мониторинг HDInsight Hadoop
* HDInsight HBase Monitoring
* мониторинг HDInsight Interactive Query;
* Мониторинг Kafka HDInsight
* Мониторинг HDInsight Spark
* Мониторинг HDInsight Storm

Инструкции по установке решения управления см. в [этом](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) разделе. Для эксперимента установите решение HDInsight Hadoop Monitoring. Когда это будет сделано, вы видите **плитку HDInsightHadoop,** перечисленные в **Резюме.** Выберите элемент **HDInsightHadoop**. Решение HDInsightHadoop будет выглядеть примерно следующим образом:

![Решение HDInsight для мониторинга](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Так как это новый кластер, в отчете не будут отображаться никакие действия.

## <a name="configuring-performance-counters"></a>Настройка счетчиков производительности

Монитор Azure также поддерживает сбор и анализ метрик производительности узлов в кластере. Для получения дополнительной информации о включении и настройке этой функции [см.](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)

## <a name="cluster-auditing"></a>Кластерный аудит

Мониторинг кластера поддержки HDInsight с помощью журналов Azure Monitor, импортируя следующие типы журналов:

* `log_gateway_audit_CL`- в этой таблице представлены журналы аудита из узлов кластерных шлюзов, которые показывают успешные и неудачные попытки входа.
* `log_auth_CL`- эта таблица обеспечивает sSH журналы с успешными и неудачными попытками входа.
* `log_ambari_audit_CL`- в этой таблице представлены журналы аудита из Амбари.
* `log_ranger_audti_CL`- в этой таблице представлены журналы аудита от Apache Ranger на кластерах ESP.

## <a name="next-steps"></a>Дальнейшие действия

* [Журналы мониторинга запросов Azure Monitor для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
