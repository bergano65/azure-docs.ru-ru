---
title: Мониторинг кластеров Azure HDInsight с помощью Log Analytics
description: Узнайте, как использовать Azure Log Analytics для мониторинга заданий, выполняемых в кластере HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 2af68c0b9deb9d5b065f5fae42cf4bf927f0c671
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386460"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Использование Azure Log Analytics для мониторинга кластеров HDInsight

Узнайте, как включить Azure Log Analytics для мониторинга операций кластера Hadoop в HDInsight и как добавить решение мониторинга HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) — это служба в Azure Monitor, которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Рабочая область Log Analytics**. Рабочую область можно представить как уникальную среду Log Analytics с собственным репозиторием данных, источниками данных и решениями. С инструкциями можно ознакомиться в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Кластер Azure HDInsight**. Сейчас вы можете использовать Log Analytics со следующими типами кластеров HDInsight:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> Для повышения производительности рекомендуется разместить кластер HDInsight и рабочую область Log Analytics в одном регионе. Azure Log Analytics доступна не во всех регионах Azure.

## <a name="enable-log-analytics-by-using-the-portal"></a>Включение Log Analytics с помощью портала

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. Откройте кластер HDInsight на портале Azure.
2. В левой области щелкните **Мониторинг**.
3. В области справа щелкните **Включить** и выберите имеющуюся рабочую область Log Analytics. Затем нажмите кнопку **Сохранить**.

    ![Включение мониторинга кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Включение мониторинга кластеров HDInsight")

    Параметр сохраняется в течение нескольких секунд.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Включение Log Analytics с помощью Azure PowerShell

Включить Log Analytics можно с помощью Azure PowerShell. Для этого используется следующий командлет:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

[Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Для отключения используется следующий командлет:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

[Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Установка решений по управлению кластерами HDInsight

HDInsight предоставляет решения по управлению для кластеров, которые можно добавлять в Azure Log Analytics. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) расширяют функциональные возможности Log Analytics, предоставляя дополнительные средства анализа и работы с данными. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения.

Вот доступные решения HDInsight:

* Мониторинг HDInsight Hadoop
* HDInsight HBase Monitoring
* мониторинг HDInsight Interactive Query;
* Мониторинг Kafka HDInsight
* Мониторинг HDInsight Spark
* Мониторинг HDInsight Storm

Инструкции по установке решения управления см. в [этом](../azure-monitor/insights/solutions.md#install-a-management-solution) разделе. Чтобы поэкспериментировать, установите решение мониторинга HDInsight Hadoop. После установки вы увидите элемент **HDInsightHadoop** под разделом **сводки**. Выберите элемент **HDInsightHadoop**. Решение HDInsightHadoop будет выглядеть примерно следующим образом:

![Решение HDInsight для мониторинга](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Так как это новый кластер, в отчете не будут отображаться никакие действия.

## <a name="next-steps"></a>Дополнительная информация

* [Запрос в Azure Log Analytics для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
