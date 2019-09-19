---
title: Настройка конфигураций кластера Azure HDInsight с помощью начальной загрузки
description: Узнайте, как программно настроить конфигурацию кластера HDInsight с помощью .NET, PowerShell и шаблонов диспетчер ресурсов.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098670"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Настройка кластеров HDInsight с помощью начальной загрузки

Сценарии начальной загрузки позволяют программно устанавливать и настраивать компоненты в Azure HDInsight.

Существует три подхода к установке параметров файла конфигурации при создании кластера HDInsight:

* Использование Azure PowerShell
* Использование пакета SDK для .NET
* Использование шаблона Azure Resource Manager

Например, с помощью этих программных методов можно настроить параметры в следующих файлах:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (конфигурация kafka-broker).

Сведения об установке дополнительных компонентов в кластере HDInsight во время создания см. в статье [Настройка кластеров hdinsight с помощью действия сценария (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Предварительные требования

* При использовании PowerShell потребуется [модуль AZ](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

Следующий код PowerShell настраивает конфигурацию [Apache Hive](https://hive.apache.org/).

> [!IMPORTANT]  
> Параметр `Spark2Defaults` может потребоваться использовать с командлетом [Add-аздинсигхтконфигвалуе](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). В параметр можно передать пустые значения, как показано в примере кода ниже.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. В меню слева щелкните **Кластеры HDInsight**. Если вы не видите этот пункт, сначала щелкните **Все службы**.
3. Щелкните кластер, созданный с помощью сценария PowerShell.
4. Щелкните **Панель мониторинга** в верхней части колонки, чтобы открыть пользовательский интерфейс Ambari.
5. Щелкните **Hive** в меню слева.
6. Щелкните **HiveServer2** в разделе **Сводка**.
7. Выберите вкладку **Configs** (Конфигурации).
8. Щелкните **Hive** в меню слева.
9. Выберите вкладку **Advanced** (Дополнительно).
10. Прокрутите вниз и разверните раздел **Advanced hive-site**(Расширенный сайт Hive).
11. Найдите в этом разделе **hive.metastore.client.socket.timeout** .

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
Ознакомьтесь с разделом [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

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

![Hadoop настраивает шаблон Azure Resource Manager начальной загрузки кластера](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>См. также

* [Создание кластеров Apache Hadoop в hdinsight][hdinsight-provision-cluster] содержит инструкции по созданию кластера HDInsight с помощью других настраиваемых параметров.
* [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script]
* [Установка и использование Apache Spark в кластерах HDInsight][hdinsight-install-spark]
* [Установка и использование Apache Giraph в кластерах HDInsight под управлением Windows](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Этапы создания кластера"

## <a name="appendix-powershell-sample"></a>Приложение Пример для PowerShell

Этот сценарий PowerShell создает кластер HDInsight и настраивает параметр Hive. Не забудьте ввести значения для `$nameToken`, `$httpPassword`и `$sshPassword`.

> [!WARNING]  
> Тип учетной записи хранения `BlobStorage` нельзя использовать для кластеров HDInsight.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

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
