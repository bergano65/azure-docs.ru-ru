---
title: Выполнение заданий Apache Sqoop с помощью .NET и HDInsight в Azure
description: Узнайте, как использовать пакет SDK для HDInsight .NET для выполнения импорта и экспорта Apache Sqoop между кластером Apache Hadoop и базой данных SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76157072"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Выполнение заданий Apache Sqoop с помощью пакета SDK для .NET для Apache Hadoop в HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать пакет SDK Azure HDInsight для .NET, чтобы выполнять задания Apache Sqoop в HDInsight для импорта и экспорта между кластером HDInsight и базой данных SQL Azure или базой данных SQL Server.

## <a name="prerequisites"></a>Предварительные требования

* Завершение [настройки тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) с [помощью Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Знакомство с Sqoop. Дополнительные сведения см. в разделе [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Использование Sqoop в кластерах HDInsight с пакетом SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. В этом разделе вы создадите консольное приложение C# для экспорта `hivesampletable` в таблицу базы данных SQL Azure, созданную на основе необходимых компонентов.

## <a name="set-up"></a>Настройка

1. Запустите Visual Studio и создайте консольное приложение C#.

1.  > Последовательно выберите **инструменты** > **Диспетчер пакетов NuGet****консоль диспетчера пакетов** и выполните следующую команду:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Экспорт Sqoop

Из Hive в SQL Server.  В этом примере данные из таблицы Hive `hivesampletable` экспортируются в `mobiledata` таблицу базы данных SQL.

1. Используйте следующий код в файле Program.cs. Измените код, чтобы задать значения для `ExistingClusterName`, и. `ExistingClusterPassword`

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

Из SQL Server в службу хранилища Azure. Этот пример зависит от выполненной операции экспорта.  В этом примере данные из `mobiledata` таблицы в базе данных SQL импортируются `wasb:///tutorials/usesqoop/importeddata` в каталог в учетной записи хранения по умолчанию кластера.

1. Замените приведенный выше код в `//sqoop start //sqoop end` блоке следующим кодом:

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

* Групповой экспорт. соединитель Sqoop, используемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, в настоящее время не поддерживает операции вставки.

* Пакетная обработка. с помощью `-batch` параметра Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Apache Oozie с HDInsight](../hdinsight-use-oozie-linux-mac.md): используйте действие Sqoop в рабочем процессе Oozie.
* [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.
