---
title: Управление кластерами Apache Hadoop в HDInsight с помощью PowerShell — Azure
description: Узнайте, как осуществлять управление кластерами Apache Hadoop в HDInsight с использованием Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tylerfox
ms.openlocfilehash: f7c894bcb3183714a76578a6750c8e85493e3094
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508080"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Управление кластерами Apache Hadoop в HDInsight с помощью Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье вы узнаете, как управлять [Apache Hadoop](https://hadoop.apache.org/) кластеров в Azure HDInsight с помощью модуля Azure PowerShell Az. Список командлетов HDInsight PowerShell, см. в разделе [Az.HDInsight ссылку](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Установите [модуль Az](https://docs.microsoft.com/powershell/azure/overview) для PowerShell.

## <a name="create-clusters"></a>Создание кластеров
Ознакомьтесь с разделом [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Получение списка кластеров
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
Масштабирование кластера позволяет изменить количество рабочих узлов в кластере, который работает под управлением Azure HDInsight. При этом не требуется повторно создавать кластер.

Ниже представлены возможности, связанные с изменением количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight.

* Apache Hadoop

    Вы можете легко увеличить количество рабочих узлов в работающем кластере Hadoop. Это не помешает обработке заданий в состоянии ожидания и выполнения. В ходе выполнения операции можно также отправлять новые задания. Сбои операции масштабирования обрабатываются корректно, поэтому кластер всегда пребывает в функциональном состоянии.

    Если уменьшить масштаб кластера Hadoop, сократив количество узлов данных, некоторые службы в нем будут перезапущены. Перезапуск служб приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.
* Apache HBase

    Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере HBase. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Но их также можно сбалансировать вручную, выполнив вход на головной узел кластера и выполнив приведенные ниже команды в окне командной строки.

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере Storm. Но после успешного завершения операции масштабирования потребуется повторная балансировка топологии.

    Повторную балансировку можно выполнить двумя способами:

  * с помощью веб-интерфейса Storm;
  * с помощью программы командной строки.

    Дополнительные сведения см. в [документации по Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    В кластере HDInsight доступен веб-интерфейс Storm.

    ![HDInsight, storm, масштабирование, перераспределение](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Ниже приведен пример использования команды CLI для повторной балансировки топологии Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Предоставление и отмена доступа
В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ. Для отмены:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Для предоставления:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Предоставляя или отменяя доступ, вы сбрасываете имя пользователя кластера и его пароль.

Предоставить и отменить доступ можно также на портале. См. в разделе [управление Apache кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Обновление учетных данных пользователя HTTP
Процедура та же, что и при предоставлении или отмене доступа по протоколу HTTP. Если кластеру был предоставлен доступ по протоколу HTTP, то сначала его необходимо отменить.  После этого предоставьте доступ с новыми учетными данными пользователя HTTP.

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

См. в разделе [отправка данных в HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>См. также

* [Справочная документация по командлетам HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Управление кластерами Apache Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md)
* [Администрирование HDInsight с помощью интерфейса командной строки](hdinsight-administer-use-command-line.md)
* [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Отправка заданий Apache Hadoop программными средствами](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Приступая к работе с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
