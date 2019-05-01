---
title: Журналы использования Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как использовать журналы Azure Azure Monitor для мониторинга заданий, выполняемых в кластере HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 0c37ff3c62817d238a94d5bbeae90c67663a6029
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689842"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Журналы использования Azure Monitor для мониторинга кластеров HDInsight

Узнайте, как включить журналы Azure Monitor для мониторинга операций кластера Hadoop в HDInsight и как добавить решение для мониторинга HDInsight.

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это служба в Azure Monitor, которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

* **Рабочая область Log Analytics**. Эта рабочая область можно считать уникальную среду журналы Azure Monitor с свой собственный репозиторий данных, источники данных и решениями. С инструкциями можно ознакомиться в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Кластер Azure HDInsight**. В настоящее время журналы Azure Monitor можно использовать со следующими типами кластеров HDInsight:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Модуль Azure PowerShell Az**.  См. в разделе [введение в новый модуль Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Для повышения производительности рекомендуется разместить кластер HDInsight и рабочую область Log Analytics в одном регионе. Журналы Azure Monitor доступна не во всех регионах Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Включить журналы Azure Monitor с помощью портала

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. Войдите на [портале Azure](https://portal.azure.com).

2. В меню слева выберите **Все службы**.

3. В разделе **АНАЛИТИКА** выберите **Кластеры HDInsight**.

4. Выберите свой кластер из списка.

5. Слева в разделе **Мониторинг** выберите **Operations Management Suite**.

6. В главном представлении в разделе **Мониторинг OMS** щелкните **Включить**.

7. В раскрывающемся списке **Выберите рабочую область** выберите существующую рабочую область Log Analytics.

8. Щелкните **Сохранить**.  Параметр сохраняется в течение нескольких секунд.

    ![Включение мониторинга кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Включение мониторинга кластеров HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Включить журналы Azure Monitor с помощью Azure PowerShell

Вы можете включить журналы Azure Monitor с помощью модуля Azure PowerShell Az [Enable AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) командлета.

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

Чтобы отключить функцию [Disable AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) командлета:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Установка решений по управлению кластерами HDInsight

HDInsight предоставляет решения по управлению для кластеров, которые можно добавить для журналов Azure Monitor. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) расширяют функциональные возможности журналы Azure Monitor, предоставляя дополнительные данные и средства анализа. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения.

Вот доступные решения HDInsight:

* Мониторинг HDInsight Hadoop
* HDInsight HBase Monitoring
* мониторинг HDInsight Interactive Query;
* Мониторинг Kafka HDInsight
* Мониторинг HDInsight Spark
* Мониторинг HDInsight Storm

Инструкции по установке решения управления см. в [этом](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) разделе. Чтобы поэкспериментировать, установка решения мониторинга HDInsight Hadoop. После установки вы увидите элемент **HDInsightHadoop** под разделом **сводки**. Выберите элемент **HDInsightHadoop**. Решение HDInsightHadoop будет выглядеть примерно следующим образом:

![Решение HDInsight для мониторинга](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Так как это новый кластер, в отчете не будут отображаться никакие действия.

## <a name="next-steps"></a>Дальнейшие действия

* [Журналы запросов Azure Monitor для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
