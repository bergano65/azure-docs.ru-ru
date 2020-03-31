---
title: Выполнение заданий Apache Sqoop с помощью .NET и HDInsight в Azure
description: Узнайте, как использовать HDInsight .NET SDK для запуска импорта и экспорта Apache Sqoop между кластером Apache Hadoop и базой данных Azure S'L.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157072"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Выполнение заданий Apache Sqoop с помощью пакета SDK для .NET для Apache Hadoop в HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Azure HDInsight .NET SDK для выполнения заданий Apache Sqoop в HDInsight для импорта и экспорта между кластером HDInsight и базой данных Azure S'L или базой данных сервера S'L.

## <a name="prerequisites"></a>Предварительные требования

* Завершение [настройки тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) от [Use Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* [Визуальная студия](https://visualstudio.microsoft.com/vs/community/).

* Знакомство с Sqoop. Для получения дополнительной информации [см.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Использование Sqoop в кластерах HDInsight с пакетом SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. В этом разделе создается консольное `hivesampletable` приложение для экспорта в таблицу базы данных Azure S'L, созданную из предпосылок.

## <a name="set-up"></a>Настройка

1. Запустите Visual Studio и создайте консольное приложение для СЗ.

1. Перейдите к**консоли** менеджера**пакетов** >  **Инструментов** > NuGet и запустите следующую команду:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Экспорт Sqoop

От Улейа до сервера S'L.  Этот пример экспортирует данные из таблицы Hive `hivesampletable` в таблицу `mobiledata` базы данных S'L.

1. Используйте следующий код в файле Program.cs. Отредите код, чтобы `ExistingClusterName`установить `ExistingClusterPassword`значения для и .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Нажмите клавишу **F5**, чтобы запустить программу.

## <a name="sqoop-import"></a>Импорт Sqoop

От сервера S'L до хранилища Azure. Этот пример зависит от вышеуказанного экспорта, который был выполнен.  Этот пример импортирует `mobiledata` данные из таблицы базы данных S'L в `wasb:///tutorials/usesqoop/importeddata` каталог учетной записи хранилища данных кластера по умолчанию.

1. Замените приведенный `//sqoop start //sqoop end` выше код в блоке следующим кодом:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Нажмите клавишу **F5**, чтобы запустить программу.

## <a name="limitations"></a>Ограничения

Для HDInsight под управлением Linux действуют следующие ограничения:

* Массовый экспорт: разъем Sqoop, используемый для экспорта данных в Microsoft S'L Server или базу данных Azure S'L, в настоящее время не поддерживает объемные вставки.

* Пакетирование: С `-batch` помощью коммутатора, Sqoop выполняет несколько вставок вместо пакетирования вставки операций.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Используйте Apache Oozie с HDInsight:](../hdinsight-use-oozie-linux-mac.md)Используйте действие Sqoop в рабочем процессе Oozie.
* [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.
