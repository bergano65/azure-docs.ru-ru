---
title: Использование Apache Hive с PowerShell в HDInsight — Azure
description: Использование PowerShell для выполнения запросов Hive в Apache Hadoop в HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/23/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 108a3e7d899eef4ca78ae7507bf4852b861e74d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095529"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Выполнение запросов Apache Hive с помощью PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

В этом документе приведен пример использования Azure PowerShell в режиме группы ресурсов Azure для выполнения запросов Hive в Apache Hadoop в кластере HDInsight.

> [!NOTE]  
> В этом документе не приводится подробное описание процессов, которые выполняют инструкции HiveQL, используемые в примерах. Информацию об операторах HiveQL, используемых в этом примере, см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Apache Hadoop на базе Linux в кластере HDInsight версии 3.4 или более поздней.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Клиент с Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать запросы Hive в HDInsight. Во внутренних командлет использует вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) в кластере HDInsight.

При выполнении запросов Hive на удаленном кластере HDInsight используются следующие командлеты:

* `Connect-AzAccount`: выполняет аутентификацию Azure PowerShell для подписки Azure.
* `New-AzHDInsightHiveJobDefinition`: создает *определение задания* с использованием заданных операторов HiveQL.
* `Start-AzHDInsightJob`: отправляет определение задания в HDInsight и запускает задание. Будет возвращен объект *задания*.
* `Wait-AzHDInsightJob`: проверяет состояние задания с помощью объекта-задания. Он ожидает завершения задания или превышения времени ожидания.
* `Get-AzHDInsightJobOutput`: используется для получения выходных данных задания.
* `Invoke-AzHDInsightHiveJob`: используется для выполнения операторов HiveQL. Этот командлет блокирует выполнение запроса, после чего возвращаются результаты.
* `Use-AzHDInsightCluster`: указывает, что при выполнении команды `Invoke-AzHDInsightHiveJob` должен использоваться текущий кластер.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как `hivejob.ps1`.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #HiveQL
    #Note: set hive.execution.engine=tez; is not required for
    #      Linux-based HDInsight
    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

    #Create an HDInsight Hive job definition
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

    #Submit the job to the cluster
    Write-Host "Start the Hive job..." -ForegroundColor Green

    $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

    #Wait for the Hive job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

    # Print the output
    Write-Host "Display the standard output..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $hiveJob.JobId `
        -HttpCredential $creds
    ```

2. Откройте новую командную строку **Azure PowerShell** . Перейдите к расположению файла `hivejob.ps1`, а затем используйте следующую команду для запуска скрипта:

        .\hivejob.ps1

    При запуске скрипта будет предложено ввести имя кластера, данные HTTPS и учетной записи администратора кластера. Вам также может быть предложено выполнить вход в свою подписку Azure.

3. Когда задание завершается, возвращается текст следующего содержания:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Как уже упоминалось, `Invoke-Hive` можно использовать для отправки запроса и ожидания ответа. Чтобы увидеть работу команды Invoke-Hive, используйте следующий сценарий:

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    # Set the cluster to use
    Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds

    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
    Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "statusout" `
        -Query $queryString
    ```

    Выходные данные выглядят так:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Для более длинных запросов HiveQL вы можете использовать командлет Azure PowerShell **Here-Strings** или файлы сценариев HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет `Invoke-Hive` для запуска файла скрипта HiveQL. Файл скрипта HiveQL необходимо передать в wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Дополнительные сведения о командлете **Here-Strings** см. <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">здесь</a>.

## <a name="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются при завершении задания, просмотрите журналы ошибок. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующий текст в конце файла `hivejob.ps1`, сохраните его, а затем запустите снова.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Этот командлет возвращает информацию, которая записывается в STDERR на сервере при обработке задания.

## <a name="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a name="next-steps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)
