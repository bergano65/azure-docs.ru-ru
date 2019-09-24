---
title: Использование журналов Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для мониторинга заданий, выполняемых в кластере HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: a693b14bb61eb52a09ab1f1ecd5d00b339357d5d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240372"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Использование журналов Azure Monitor для мониторинга кластеров HDInsight

Узнайте, как включить журналы Azure Monitor для мониторинга операций кластера Hadoop в HDInsight и добавить решение для мониторинга HDInsight.

[Azure Monitor журналы](../log-analytics/log-analytics-overview.md) — это служба в Azure Monitor, которая отслеживает облачные и локальные среды, чтобы поддерживать их доступность и производительность. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Рабочая область Log Analytics**. Эту рабочую область можно рассматривать как уникальную Azure Monitor среду журналов с собственным репозиторием данных, источниками данных и решениями. С инструкциями можно ознакомиться в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Кластер Azure HDInsight**. В настоящее время журналы Azure Monitor можно использовать со следующими типами кластеров HDInsight:

  * Hadoop
  * HBase
  * Интерактивные запросы
  * Kafka
  * Spark
  * Storm

  Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell AZ Module**.  См. статью [Знакомство с новым модулем Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Для повышения производительности рекомендуется разместить кластер HDInsight и рабочую область Log Analytics в одном регионе. Журналы Azure Monitor недоступны во всех регионах Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Включение журналов Azure Monitor с помощью портала

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. В [портал Azure](https://portal.azure.com/)выберите свой кластер.  Инструкции см. в разделе [Отображение кластеров](./hdinsight-administer-use-portal-linux.md#showClusters). Кластер открывается на новой странице портала.

1. Слева в разделе **Мониторинг** выберите **Operations Management Suite**.

1. В главном представлении в разделе **Мониторинг OMS** щелкните **Включить**.

1. В раскрывающемся списке **Выберите рабочую область** выберите существующую рабочую область Log Analytics.

1. Щелкните **Сохранить**.  Параметр сохраняется в течение нескольких секунд.

    ![Включение мониторинга кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Включение мониторинга кластеров HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Включение журналов Azure Monitor с помощью Azure PowerShell

Вы можете включить Azure Monitor журналов с помощью командлета Azure PowerShell AZ Module [Enable-аздинсигхтоператионсманажементсуите](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) .

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Чтобы отключить, используйте командлет [Disable-аздинсигхтоператионсманажементсуите](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Установка решений по управлению кластерами HDInsight

HDInsight предоставляет решения по управлению кластерами, которые можно добавить для Azure Monitor журналов. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) добавляют функции в журналы Azure Monitor, предоставляя дополнительные инструменты для работы с данными и анализа. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения.

Вот доступные решения HDInsight:

* Мониторинг HDInsight Hadoop
* Мониторинг HDInsight HBase
* мониторинг HDInsight Interactive Query;
* Мониторинг Kafka HDInsight
* Мониторинг HDInsight Spark
* Мониторинг HDInsight Storm

Инструкции по установке решения управления см. в [этом](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) разделе. Чтобы поэкспериментировать, установите решение для мониторинга Hadoop в HDInsight. После установки вы увидите элемент **HDInsightHadoop** под разделом **сводки**. Выберите элемент **HDInsightHadoop**. Решение HDInsightHadoop будет выглядеть примерно следующим образом:

![Решение HDInsight для мониторинга](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Так как это новый кластер, в отчете не будут отображаться никакие действия.

## <a name="configuring-performance-counters"></a>Настройка счетчиков производительности

Azure Monitor также поддерживает сбор и анализ метрик производительности для узлов в кластере. Дополнительные сведения о включении и настройке этого компонента см. [в разделе источники данных о производительности Linux в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Аудит кластера

HDInsight поддерживает аудит кластеров с помощью журналов Azure Monitor, импортируя следующие типы журналов:

* `log_gateway_audit_CL`— Эта таблица предоставляет журналы аудита из узлов шлюза кластера, которые показывают успешные и неудачные попытки входа.
* `log_auth_CL`— Эта таблица предоставляет журналы SSH с успешным и неудачным попытками входа.
* `log_ambari_audit_CL`— Эта таблица предоставляет журналы аудита из Ambari.
* `log_ranger_audti_CL`— Эта таблица предоставляет журналы аудита из Apache Ranger в кластерах ESP.

## <a name="next-steps"></a>Следующие шаги

* [Запрос журналов Azure Monitor для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
