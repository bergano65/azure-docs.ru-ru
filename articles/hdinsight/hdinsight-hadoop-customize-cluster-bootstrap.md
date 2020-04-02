---
title: Настройка конфигураций кластеров Azure HDInsight с помощью загрузки
description: Узнайте, как настроить конфигурацию кластера HDInsight программно, используя шаблоны .NET, PowerShell и Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529350"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Настройка кластеров HDInsight с помощью начальной загрузки

Скрипты Bootstrap позволяют устанавливать и настраивать компоненты в Azure HDInsight программно.

Существует три подхода к настройке параметров файла конфигурации по мере создания кластера HDInsight:

* Использование Azure PowerShell
* Использование пакета SDK для .NET
* Использование шаблона Azure Resource Manager

Например, используя эти программные методы, можно настроить параметры в следующих файлах:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* Hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (конфигурация kafka-broker).

Для получения информации об установке дополнительных компонентов в кластере HDInsight во время создания, [см.](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="prerequisites"></a>Предварительные требования

* При использовании PowerShell вам понадобится [модуль Az.](https://docs.microsoft.com/powershell/azure/overview)

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

Следующий код PowerShell настраивает конфигурацию [Apache Hive](https://hive.apache.org/).

> [!IMPORTANT]  
> Параметр `Spark2Defaults` может потребоваться использовать с [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Вы можете передать пустые значения параметру, как показано в примере кода ниже.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Полный рабочий сценарий PowerShell можно найти в разделе [Приложение. Пример PowerShell](#appendix-powershell-sample).

**Чтобы проверить изменения, выполните следующие действия:**

1. Перейдите `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` туда, где находится название кластера.
1. Из левого меню, перейдите к **Hive** > **Configs** > **Advanced**.
1. Расширить **Расширенный улей-сайт**.
1. Найдите **hive.metastore.client.socket.timeout** и подтвердите значение **90s.**

Вот еще несколько примеров изменения других файлов конфигурации:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Использование пакета SDK для .NET

Смотрите [Azure HDInsight SDK для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager

В шаблоне Resource Manager можно использовать начальную загрузку:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop настраивает шаблон кластерного загрузок Azure Resource Manager](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Пример фрагмента шаблона управления ресурсами для переключения конфигурации в spark2-defaults для периодической очистки журналов событий из хранилища.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>См. также

* статью [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md), в которой даются указания по созданию кластера HDInsight с использованием других настраиваемых параметров.
* [Разработка скриптов для HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](spark/apache-spark-jupyter-spark-sql-use-portal.md).
* [Установка и использование Apache Giraph на кластерах HDInsight.](hdinsight-hadoop-giraph-install.md)

## <a name="appendix-powershell-sample"></a>Приложение. Пример PowerShell

Этот скрипт PowerShell создает кластер HDInsight и настраивает настройки Hive. Обязательно введите значения `$nameToken` `$httpPassword`для `$sshPassword`, и .

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
