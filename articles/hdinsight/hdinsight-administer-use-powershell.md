---
title: Управление кластерами Apache Hadoop с помощью PowerShell - Azure HDInsight
description: Узнайте, как осуществлять управление кластерами Apache Hadoop в HDInsight с использованием Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560360"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Управление кластерами Apache Hadoop в HDInsight с помощью Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье вы узнаете, как управлять кластерами [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight с помощью модуля Azure PowerShell Az. Список cmdlets HDInsight PowerShell можно узнать из [ссылки Az.HDInsight.](https://docs.microsoft.com/powershell/module/az.hdinsight)

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Установите [модуль Az](https://docs.microsoft.com/powershell/azure/overview) для PowerShell.

## <a name="create-clusters"></a>Создание кластеров

Ознакомьтесь с разделом [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>список кластеров

Чтобы получить список всех кластеров в текущей подписке, используйте следующую команду:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Отображение кластеров

Чтобы отобразить сведения о конкретном кластере в текущей подписке, используйте следующую команду:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Удаление кластеров

Используйте следующую команду для удаления кластера:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Удаление группы ресурсов приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Масштабирование кластеров

Масштабирование кластера позволяет изменить количество рабочих узлов в кластере, который работает под управлением Azure HDInsight. При этом не требуется повторно создавать кластер. Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Для получения дополнительной информации о [Scale HDInsight clusters](./hdinsight-scaling-best-practices.md)кластерах масштабирования см.

## <a name="update-http-user-credentials"></a>Обновление учетных данных пользователя HTTP

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) устанавливает учетные данные шлюза HTTP кластера Azure HDInsight.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Поиск учетной записи хранения по умолчанию

В следующем сценарии PowerShell показано получение имени учетной записи хранения по умолчанию и связанной информации.

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Поиск группы ресурсов

В режиме Resource Manager каждый кластер HDInsight относится к группе ресурсов Azure.  Поиск группы ресурсов:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Отправка заданий

**Отправка заданий MapReduce**

См. статью [Выполнение примеров MapReduce, включенных в HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Отправка заданий Apache Hive**

См. статью о [выполнении запросов Apache Hive с помощью PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Отправка заданий Apache Sqoop**

См. статью об [использовании Apache Sqoop с HDInsight](hadoop/hdinsight-use-sqoop.md).

**Отправка заданий Apache Oozie**

См. статью об [использовании Apache Oozie с Apache Hadoop для определения и выполнения рабочего процесса в HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Отправка данных в хранилище BLOB-объектов Azure

Ознакомьтесь со статьей [Отправка данных в HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>См. также

* [Az.HDInsight cmdlets](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Управление кластерами Apache Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md)
* [Администрирование HDInsight с помощью интерфейса командной строки](hdinsight-administer-use-command-line.md)
* [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Отправка заданий Apache Hadoop программными средствами](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Начало работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
