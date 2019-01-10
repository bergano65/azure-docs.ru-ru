---
title: Выполнение заданий Apache Sqoop с помощью PowerShell и Azure HDInsight
description: Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Apache Sqoop, импорта и экспорта между кластером Apache Hadoop и базой данных Azure SQL.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: c3b7695b7a67a33df93b0216cfd02f762e412158
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635720"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Выполнение заданий Apache Sqoop с помощью Azure PowerShell для Apache Hadoop в HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Azure PowerShell для выполнения заданий Apache Sqoop в Azure HDInsight: импорт и экспорт между кластером HDInsight и базой данных SQL Azure или базой данных SQL Server.

> [!NOTE]  
> Инструкции, приведенные в этой статье, можно использовать для кластера HDInsight под управлением Windows или Linux. Но эти действия можно выполнять только из клиента Windows. Чтобы выбрать другие методы, используйте селектор вкладок в верхней части этой статьи. 

## <a name="prerequisites"></a>Предварительные требования 
Перед началом работы с этим руководством необходимо иметь следующее:

* Рабочая станция с Azure PowerShell.
* Кластер Apache Hadoop в HDInsight. Дополнительные сведения см. в разделе [Создание кластера и базы данных SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-apache-sqoop-by-using-powershell"></a>Запуск Apache Sqoop с помощью PowerShell
Следующий сценарий PowerShell выполняет предварительную обработку исходного файла, а затем экспортирует его в базу данных SQL Azure:

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #endregion

    #region - pre-process the source file

    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"

    # Define the connection string
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    # Create block blob objects referencing the source and destination blob.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $defaultStorageAccountName
    $storageContainer = ($storageAccount |Get-AzureStorageContainer -Name $defaultBlobContainerName).CloudBlobContainer
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)

    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")

        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)

            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }

        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }

    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    #endregion

    #region - export the log file from the cluster to the SQL database

    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
    $sqljdbcdriver = "/user/oozie/share/lib/sqoop/sqljdbc41.jar"

    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
        -Files $sqljdbcdriver

    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    #endregion

## <a name="limitations"></a>Ограничения
Для HDInsight под управлением Linux действуют следующие ограничения:

* Массовый экспорт: соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.

* Пакетная обработка: когда для вставок применяется параметр `-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки. 

## <a name="next-steps"></a>Дополнительная информация
Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Apache Oozie с HDInsight](../hdinsight-use-oozie.md). Используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight](../hdinsight-analyze-flight-delay-data.md). Используйте Apache Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Отправка данных в HDInsight](../hdinsight-upload-data.md). Узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
