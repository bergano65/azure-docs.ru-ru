---
title: Переход с SQL Server на Управляемый экземпляр Базы данных SQL Azure с помощью Database Migration Service и PowerShell | Документация Майкрософт
description: Сведения о переходе с локального сервера SQL Server на Управляемый экземпляр Базы данных SQL Azure с помощью Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: d83410efd26f8c2078d3abdb01d061db0b83d33d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233714"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Миграция локального SQL Server в управляемый экземпляр базы данных SQL Azure с помощью Azure PowerShell
В этой статье выполняется перенос **Adventureworks2016** базы данных, восстановленной на локальном экземпляре SQL Server 2005 или выше в базе данных SQL управляемого экземпляра с помощью Microsoft Azure PowerShell. Вы можете перенести базы данных из экземпляра SQL Server в локальной базы данных SQL управляемого экземпляра с помощью `Az.DataMigration` модуля в Microsoft Azure PowerShell.

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
>
> * Создайте группу ресурсов.
> * создание экземпляра Azure Database Migration Service;
> * Создайте проект миграции в экземпляре Azure Database Migration Service.
> * выполнение миграции.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Эта статья содержит сведения о том, как выполнять автономно и миграции.

## <a name="prerequisites"></a>Технические условия

Для выполнения этих действий вам потребуется следующее:

* [SQL Server 2016 или более поздней версии](https://www.microsoft.com/sql-server/sql-server-downloads) (любой выпуск).
* Локальная копия **AdventureWorks2016** базы данных, которая доступна для загрузки [здесь](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* При установке SQL Server Express включите протокол TCP/IP, который отключен по умолчанию. Для этого выполните инструкции в разделе [Использование диспетчера конфигурации SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) статьи "Включение или отключение сетевого протокола сервера".
* [Настройте брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
* Управляемый экземпляр базы данных SQL Azure. Можно создать управляемый экземпляр базы данных SQL Azure, ознакомившись со сведениями в статье [создать управляемый экземпляр базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Чтобы скачать и установить [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) версии 3.3 или более поздней версии.
* Виртуальной сети Azure (VNet) создан с помощью модели развертывания Azure Resource Manager предоставляет Azure Database Migration Service с сайтами подключением к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Выполненную оценку миграции локальной базы данных и схемы с помощью Data Migration Assistant, как описано в статье [выполнение оценки миграции SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Чтобы скачать и установить `Az.DataMigration` модуля (версия 0.7.2 или более поздней версии) из коллекции PowerShell с помощью [командлета PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Чтобы убедиться, что учетные данные, используемые для подключения к исходному экземпляру SQL Server имеют [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) разрешение.
* Чтобы убедиться, что учетные данные, используемые для подключения к целевой базе данных SQL Azure управляемый экземпляр имеет разрешение на базу данных системы УПРАВЛЕНИЯ на целевые базы данных экземпляра управляемые базы данных SQL Azure.

    > [!IMPORTANT]
    > В отношении миграцию ресурсов в online, вы должны уже настроили учетные данные Azure Active Directory. Дополнительные сведения см. в статье [использовать портал для создания приложения и субъекта-службы с доступом к ресурсам Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Войдите свою подписку Microsoft Azure

Войдите подписку Azure с помощью PowerShell. Дополнительные сведения см. в статье [войдите с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) команды.

В следующем примере создается группа ресурсов, с именем *myResourceGroup* в *восточной части США* регион.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service

Вы можете создать экземпляр Azure Database Migration Service, выполнив командлет `New-AzDataMigrationService`.
Для этого командлета нужно передать следующие обязательные параметры:

* *ResourceGroupName.* Можно использовать [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) команду, чтобы создать группу ресурсов Azure, как описано в предыдущем и указать ее имя в качестве параметра.
* *ServiceName.* Строка, соответствующая требуемому уникальному имени службы для Azure Database Migration Service.
* *Расположение.* Указывает расположение службы. Укажите расположение центра обработки данных Azure, например Западная часть США или Юго-Восточная Азия.
* *Sku.* Этот параметр соответствует имени SKU DMS. В настоящее время поддерживаются имена Sku: *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *VirtualSubnetId.* Можно использовать командлет [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) создать подсеть.

В следующем примере создается служба с именем *MyDMS* в группе ресурсов *MyDMSResourceGroup* в *восточной части США* регион с помощью виртуальной сети с именем  *MyVNET* и подсеть с именем *MySubnet*.

> [!IMPORTANT]
> Приведенный ниже фрагмент кода — для автономной миграции, которая не требует экземпляра Azure Database Migration Service, в зависимости от SKU "премиум". Значение параметра - Sku оперативную миграцию, необходимо включить номер SKU уровня "премиум".

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Создание проекта миграции

Создав экземпляр Azure Database Migration Service, создайте проект миграции. Для проекта Azure Database Migration Service необходимо иметь сведения о подключении для исходных и целевых экземпляров, а также список баз данных, которые требуется перенести как часть проекта.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Создание объекта сведений о подключении к базе данных для исходных и целевых подключений

Можно создать объект сведений о подключении базы данных с помощью `New-AzDmsConnInfo` командлет, который принимает следующие параметры:

* *ServerType.* Тип запрошенного подключения к базе данных, например SQL, Oracle или MySQL. Используйте SQL для SQL Server и SQL Azure.
* *DataSource.* Имя или IP-адресом экземпляра SQL Server или экземпляре базы данных SQL Azure.
* *AuthType.* Тип проверки подлинности для подключения. Это может быть SqlAuthentication или WindowsAuthentication.
* *TrustServerCertificate*. Этот параметр задает значение, указывающее, является ли шифроваться канал при проходе по цепочке сертификатов для проверки доверия. Значение может быть `$true` или `$false`.

В следующем примере создается объект сведений о подключении для источника SQL Server вызывается *MySourceSQLServer* с проверкой подлинности sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

В следующем примере показано создание сведений о подключении для управляемого экземпляра базы данных SQL server с именем «targetmanagedinstance.database.windows.net» с использованием проверки подлинности sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Указание баз данных для проекта миграции

Создать список `AzDataMigrationDatabaseInfo` объекты, на которые указывает баз данных как часть проекта Azure Database Migration Service, которую можно указать в качестве параметра для создания проекта. Можно использовать командлет `New-AzDataMigrationDatabaseInfo` для создания `AzDataMigrationDatabaseInfo`.

В следующем примере создается `AzDataMigrationDatabaseInfo` project для **AdventureWorks2016** базы данных и добавляет его в список, чтобы предоставить в виде параметра для создания проекта.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Создание объекта проекта

Наконец, можно создать проект Azure Database Migration Service с именем *MyDMSProject* в *восточной части США* с помощью `New-AzDataMigrationProject` и добавьте ранее созданный исходного и целевого подключения и список баз данных для миграции.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Создание и запуск задачи миграции

Затем создайте и запустите задачу Azure Database Migration Service. Эта задача требует учетные данные подключения как источник и целевой объект, а также список таблиц базы данных для миграции и информации, предоставленной с проект, созданный в качестве необходимого компонента.

### <a name="create-credential-parameters-for-source-and-target"></a>Создание параметров учетных данных для исходного и целевого подключения

Создание подключения учетные данные безопасности, как [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) объекта.

В следующем примере показано создание *PSCredential* объекты для исходной и целевой подключения, пароли, как строковые переменные *$sourcePassword* и *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Создайте объект общую папку резервной копии

Теперь создайте общую папку объект, представляющий локальный сетевой папке SMB, в которую Azure Database Migration Service может занять источника резервных копий с помощью `New-AzDmsFileShare` командлета.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Создание объекта выбранной базы данных

Следующим шагом является Выбор исходной и целевой баз данных с помощью `New-AzDmsSelectedDB` командлета.

Ниже приведен по переносу одной базы данных из SQL Server в управляемый экземпляр базы данных SQL Azure:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Если требуется всего экземпляра SQL Server lift-and-shift в базе данных SQL управляемого экземпляра, то цикл, чтобы воспользоваться всеми базами данных из источника приведен ниже. В следующем примере для $Server $SourceUserName и $SourcePassword, подробно источника SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Универсальный код ресурса (URI) SAS для контейнера службы хранилища Azure

Создайте переменную, содержащую URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Дополнительные требования к конфигурации

Существует несколько дополнительных требований, которые необходимо выполнить, но они отличаются в зависимости от выполняемой миграции или вне сети.

#### <a name="offline-migrations"></a>Автономные миграции

Для только автономные миграции выполните следующие дополнительные задачи по настройке.

* **Выберите имена для входа**. Создайте список имен входа, подлежащих миграции, как показано в следующем примере:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service, в настоящее время поддерживает только миграция имен входа SQL.

* **Выберите задания агента**. Создание списка агента заданий миграции, как показано в следующем примере:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service, в настоящее время поддерживает только задания с шагами задания подсистемы T-SQL.

#### <a name="online-migrations"></a>Online миграции

Для сети только при миграции выполните следующие дополнительные задачи по настройке.

* **Настройка приложения Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Настройка ресурсов хранилища**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Создание и запуск задачи миграции

Выполните командлет `New-AzDataMigrationTask`, чтобы создать и запустить задачу миграции.

#### <a name="specify-parameters"></a>Указание параметров

##### <a name="common-parameters"></a>Общие параметры

Независимо от того, выполнении миграции или вне сети `New-AzDataMigrationTask` командлет ожидает следующие параметры:

* *TaskType.* Для создаваемой задачи миграции для типа миграции "из SQL Server в Управляемый экземпляр Базы данных SQL Azure" ожидается тип *MigrateSqlServerSqlDbMi*. 
* *ResourceGroupName.* Имя группы ресурсов Azure, в которой будет создана задача.
* *ServiceName.* Экземпляр Azure Database Migration Service, в котором будет создана задача.
* *ProjectName.* Имя проекта Azure Database Migration Service, в котором будет создана задача. 
* *TaskName.* Имя задачи, которая будет создана. 
* *SourceConnection*. AzDmsConnInfo объект, представляющий соединение с источником SQL Server.
* *TargetConnection.* AzDmsConnInfo объект, представляющий подключение целевой базы данных управляемого экземпляра SQL Azure.
* *SourceCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к исходному серверу.
* *TargetCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к целевому серверу.
* *SelectedDatabase.* AzDataMigrationSelectedDB объект, представляющий сопоставление исходной и целевой базы данных.
* *BackupFileShare.* Объект FileShare, представляющий локальную сетевую папку, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника.
* *BackupBlobSasUri.* URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий. Узнайте, как получить URI SAS для контейнера больших двоичных объектов.
* *SelectedLogins.* Список выбранных для переноса имен для входа.
* *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

##### <a name="additional-parameters"></a>Дополнительные параметры

`New-AzDataMigrationTask` Командлет также ожидает, что параметры, которые являются уникальными для тип миграции, в автономном или интерактивном режиме, что вы выполняете.

* **Автономные миграции**. Для автономных миграций `New-AzDataMigrationTask` командлет ожидает следующие параметры:

  * *SelectedLogins.* Список выбранных для переноса имен для входа.
  * *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

* **Online миграций**. Для сети миграции `New-AzDataMigrationTask` командлет ожидает следующие параметры.

* *AzureActiveDirectoryApp*. Приложения Active Directory.
* *StorageResourceID*. Идентификатор ресурса учетной записи хранения.

#### <a name="create-and-start-an-offline-migration-task"></a>Создание и запуск задачи автономной миграции

Следующий пример создает и запускает задачу автономной миграции с именем **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Создание и запуск задачи миграции в режиме онлайн

Следующий пример создает и запускает задачу оперативная Миграция с именем **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Мониторинг миграции

Чтобы мониторинг миграции, выполните следующие задачи.

1. Объединить все сведения о миграции в переменной с именем $CheckTask.

    Чтобы объединить сведения о миграции, такие как свойства, состояние и сведения о базе данных, связанных с миграцией, используйте следующий фрагмент кода:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Используйте `$CheckTask` переменную для получения текущего состояния задачи миграции.

    Чтобы использовать `$CheckTask` переменную, чтобы получить текущее состояние задачи миграции, вы можете отслеживать выполняемую задачу миграции, запросив свойство состояния задачи, как показано в следующем примере:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Выполнение прямой миграции (online миграции только)

Оперативная миграция выполняется полное резервное копирование и восстановление баз данных и затем рабочих переходит на восстановление журналов транзакций, хранящиеся в BackupFileShare.

Если базы данных в управляемый экземпляр базы данных SQL Azure обновляется с последними данными и синхронизация с источником данных, можно выполнить прямую миграцию.

Следующий пример завершится cutover\migration. Пользователи Вызовите эту команду по своему усмотрению.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Удаление экземпляра Azure Database Migration Service

После завершения миграции вы можете удалить экземпляр Azure Database Migration Service:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Дополнительные ресурсы

Сведения о дополнительных сценариях миграции (пары исходного и целевого объектов), см. в разделе Microsoft [руководство по миграции базы данных](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о Azure Database Migration Service в этой статье [что такое Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
