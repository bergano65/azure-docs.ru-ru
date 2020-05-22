---
title: Резервное копирование и восстановление баз данных в SQL Azure для пограничных вычислений (предварительная версия)
description: Сведения о возможностях резервного копирования и восстановления в SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594003"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Резервное копирование и восстановление баз данных в SQL Azure для пограничных вычислений (предварительная версия) 

Решение SQL Azure для пограничных вычислений основано на последней версии ядра СУБД в Linux и предоставляет аналогичные возможности резервного копирования и восстановления баз данных, которые доступны в SQL Server на Linux и SQL Server, выполняющихся в контейнерах. Компонент резервного копирования и восстановления обеспечивает необходимую защиту данных, которые хранятся в базах данных SQL Azure для пограничных вычислений. Чтобы свести к минимуму риск необратимой потери данных, рекомендуется периодически создавать резервные копии баз данных для сохранения вносимых изменений на регулярной основе. Хорошо спланированная стратегия резервного копирования и восстановления защищает базы от потери данных, вызванной разными сбоями. Протестируйте стратегию, выполнив восстановление набора резервных копий и вернув в исходное состояние базу данных. Так вы будете готовы эффективно реагировать на проблемы.

Дополнительные сведения о важности резервного копирования см. в статье [Резервное копирование и восстановление баз данных SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

SQL Azure для пограничных вычислений поддерживает резервное копирование и восстановление из локального хранилища или из больших двоичных объектов Azure. Дополнительные сведения о резервном копировании и восстановлении из хранилища BLOB-объектов Azure см. в статьях [Резервное копирование и восстановление SQL Server с помощью службы хранилища BLOB-объектов Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) и [Резервное копирование в SQL Server по URL-адресу](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Резервное копирование базы данных в SQL Azure для пограничных вычислений

SQL Azure для пограничных вычислений поддерживает те же типы резервных копий, что и SQL Server. Полный список типов резервных копий, поддерживаемых в SQL Server, см. в статье [Общие сведения о резервном копировании (SQL Server)](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Базы данных, созданные в SQL Azure для пограничных вычислений, по умолчанию используют простую модель восстановления. Поэтому создать резервные копии журналов для этих баз данных нельзя. Если необходимо выполнить резервное копирование журналов в таких базах данных, администратору потребуется изменить модель восстановления базы данных на модель полного восстановления. Полный список моделей восстановления, поддерживаемых SQL Server, см. в разделе [Общие сведения о модели восстановления](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Резервное копирование на локальный диск

В приведенном ниже примере используется команда BACKUP DATABASE Transact-SQL, чтобы создать резервную копию базы данных в контейнере. В данном примере создается папка с именем backup для хранения файлов резервных копий.

1. Создайте папку для файлов резервных копий. Эту команду необходимо выполнить на узле, где выполняется контейнер SQL Azure для пограничных вычислений. В приведенной ниже команде замените **<AzureSQLEdge_Container_Name>** именем контейнера Базы данных SQL для пограничных вычислений в развертывании.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Подключитесь к экземпляру SQL Azure для пограничных вычислений с помощью SQL Server Management Studio (SSMS) или Azure Data Studio (ADS) и выполните команду BACKUP DATABASE, чтобы создать резервную копию пользовательской базы данных. В приведенном ниже примере выполняется резервное копирование базы данных *IronOreSilicaPrediction*.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. После выполнения команды и при успешном резервном копировании базы данных в разделе результатов среды SSMS или ADS появятся сообщения похожие на приведенные ниже.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Резервное копирование на URL-адрес

SQL Azure для пограничных вычислений поддерживает резервное копирование как в страничные, так и в блочные BLOB-объекты. Дополнительные сведения об этом см. в разделе [Резервное копирование в блочный и страничный BLOB-объекты](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). В приведенном ниже примере выполняется резервное копирование базы данных *IronOreSilicaPrediction* в блочный BLOB-объект. 

1. Первым шагом при настройке резервного копирования в блочные BLOB-объекты является создание маркера подписанного URL-адреса (SAS), который можно использовать для создания учетных данных SQL Server в SQL Azure для пограничных вычислений. Этот сценарий создает подписанный URL-адрес, связанный с хранимой политикой доступа. Дополнительные сведения см. в статье [Подписанные URL-адреса. Часть 1. подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Кроме того, скрипт записывает команду T-SQL, необходимую для создания учетных данных в SQL Server. В приведенном ниже сценарии предполагается, что у вас уже есть подписка Azure с учетной записью хранения и контейнером хранилища для резервных копий.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    После успешного выполнения скрипта скопируйте команду CREATE CREDENTIAL в средство запросов, подключитесь к экземпляру SQL Server и выполните команду, чтобы создать учетные данные с подписанным URL-адресом.

2. Подключитесь к экземпляру SQL для пограничных вычислений с помощью SQL Server Management Studio (SSMS) или Azure Data Studio (ADS) и создайте учетные данные, используя команду с предыдущего шага. Обязательно замените команду CREATE CREDENTIAL фактическими данными, полученными на предыдущем шаге.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Следующая команда выполняет резервное копирование базы данных *IronOreSilicaPrediction* в контейнер службы хранилища Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Восстановление базы данных в SQL Azure для пограничных вычислений

SQL Azure для пограничных вычислений поддерживает восстановление данных как с локального диска, из расположения в сети, так и из учетной записи хранилища BLOB-объектов Azure. Дополнительную информацию см. в статье [Обзор процессов восстановления (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Общие сведения о простой модели восстановления в SQL Server см. в [этой статье](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Восстановление с локального диска

Для восстановления в качестве новой базы данных с другим именем в этом примере используется резервная копия *IronOreSilicaPrediction*, выполненная в предыдущем примере.

1. Если файла резервной копии базы данных еще нет в контейнере, можно использовать приведенную ниже команду, чтобы скопировать его в контейнер. В приведенном ниже примере предполагается, что файл резервной копии находится на локальном узле и копируется в папку /var/opt/mssql/backup в контейнере SQL Azure для пограничных вычислений с именем sql1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Подключитесь к экземпляру SQL Azure для пограничных вычислений с помощью SQL Server Management Studio (SSMS) или Azure Data Studio (ADS), чтобы выполнить команду RESTORE. В приведенном ниже примере файл резервной копии **IronOrePredictDB.bak** восстанавливается для создания новой базы данных **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. После успешного выполнения операции восстановления в окне вывода появятся сообщения, похожие на приведенные ниже. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Восстановление из URL-адреса

SQL Azure для пограничных вычислений также поддерживает восстановление базы данных из учетной записи службы хранилища Azure. Восстановление можно выполнить из резервных копий блочных или страничных BLOB-объектов. В приведенном ниже примере восстанавливается файл резервной копии базы данных *IronOreSilicaPrediction_2020_04_16.bak* в блочном BLOB-объекте для создания базы данных *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


