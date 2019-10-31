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
ms.openlocfilehash: 426285340a9401aa6c84a7ee07f172eee6791d9e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163955"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Миграция локальной SQL Server в управляемый экземпляр базы данных SQL Azure с помощью Azure PowerShell
В этой статье вы перенесите базу данных **Adventureworks2016** , которая была восстановлена в локальный экземпляр SQL Server 2005 или более поздней версии, в управляемый экземпляр базы данных SQL Azure с помощью Microsoft Azure PowerShell. Вы можете перенести базы данных из локального экземпляра SQL Server в управляемый экземпляр базы данных SQL Azure с помощью модуля `Az.DataMigration` в Microsoft Azure PowerShell.

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
>
> * Создайте группу ресурсов.
> * создание экземпляра Azure Database Migration Service;
> * Создайте проект миграции в экземпляре Azure Database Migration Service.
> * выполнение миграции;

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Эта статья содержит подробные сведения о том, как выполнять оперативную и автономную миграцию.

## <a name="prerequisites"></a>Технические условия

Для выполнения этих действий вам потребуется следующее:

* [SQL Server 2016 или более поздней](https://www.microsoft.com/sql-server/sql-server-downloads) версии (любой выпуск).
* Локальная копия базы данных **AdventureWorks2016** , доступная для скачивания [здесь](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* При установке SQL Server Express включите протокол TCP/IP, который отключен по умолчанию. Для этого выполните инструкции в разделе [Использование диспетчера конфигурации SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) статьи "Включение или отключение сетевого протокола сервера".
* [Настройте брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
* Управляемый экземпляр базы данных SQL Azure. Вы можете создать управляемый экземпляр базы данных SQL Azure, следуя подробным сведениям в статье [Создание управляемого экземпляра базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Для загрузки и установки [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 или более поздней версии.
* Виртуальная сеть Azure, созданная с помощью модели развертывания Azure Resource Manager, которая предоставляет Azure Database Migration Service с подключением типа "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN. ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Завершенная оценка локальной базы данных и миграции схем с помощью Помощник по миграции данных, как описано в статье [выполнение SQL Serverной оценки миграции](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Чтобы скачать и установить модуль `Az.DataMigration` (версии 0.7.2 или более поздней) из коллекция PowerShell с помощью [командлета PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Чтобы убедиться, что учетные данные, используемые для подключения к исходному SQL Server экземпляру, имеют разрешение [Control Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Чтобы убедиться, что учетные данные, используемые для подключения к целевому управляемому экземпляру базы данных SQL Azure, имеют разрешение CONTROL DATABASE в целевых базах данных управляемых экземпляров базы данных SQL Azure.

    > [!IMPORTANT]
    > Для оперативной миграции необходимо уже учетные данные Azure Active Directory. Дополнительные сведения см. в статье [Использование портала для создания приложения Azure AD и субъекта-службы, которые имеют доступ к ресурсам](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Вход в подписку Microsoft Azure

Войдите в подписку Azure с помощью PowerShell. Дополнительные сведения см. в статье [Вход с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе " *Восточная часть США* ".

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service

Вы можете создать экземпляр Azure Database Migration Service, выполнив командлет `New-AzDataMigrationService`.
Для этого командлета нужно передать следующие обязательные параметры:

* *ResourceGroupName.* Вы можете использовать команду [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) , чтобы создать группу ресурсов Azure, как показано выше, и указать ее имя в качестве параметра.
* *ServiceName.* Строка, соответствующая нужному уникальному имени службы для Azure Database Migration Service.
* *Расположение*. Указывает расположение службы. Укажите расположение центра обработки данных Azure, например "Западная часть США" или "Юго-Восточная Азия".
* *Sku.* Этот параметр соответствует имени SKU DMS. В настоящее время поддерживаются имена SKU *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *VirtualSubnetId.* Для создания подсети можно использовать командлет [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

В следующем примере создается служба с именем *mydms* в группе ресурсов *MyDMSResourceGroup* , расположенной в регионе *восточной части США* , с использованием виртуальной сети с именем *MyVNET* и подсети *MySubnet*.

> [!IMPORTANT]
> Приведенный ниже фрагмент кода предназначен для автономной миграции, для которой не требуется экземпляр Azure Database Migration Service на основе номера SKU уровня "Премиум". Для оперативной миграции значение параметра-SKU должно включать номер SKU уровня "Премиум".

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

Объект сведений о подключении к базе данных можно создать с помощью командлета `New-AzDmsConnInfo`, который предполагает наличие следующих параметров:

* *ServerType.* Тип запрошенного подключения к базе данных, например SQL, Oracle или MySQL. Используйте SQL для SQL Server и SQL Azure.
* *DataSource.* Имя или IP-адрес экземпляра SQL Server или экземпляра базы данных SQL Azure.
* *AuthType.* Тип проверки подлинности для подключения. Это может быть SqlAuthentication или WindowsAuthentication.
* *TrustServerCertificate*. Этот параметр задает значение, указывающее, шифруется ли канал при прохождении обхода цепочки сертификатов для проверки доверия. Значение может быть `$true` или `$false`.

В следующем примере создается объект сведений о соединении для источника SQL Server с именем *мисаурцесклсервер* с использованием проверки подлинности SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

В следующем примере показано создание сведений о подключении для сервера управляемого экземпляра базы данных SQL Azure с именем "targetmanagedinstance.database.windows.net" с использованием проверки подлинности SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Указание баз данных для проекта миграции

Создайте список объектов `AzDataMigrationDatabaseInfo`, указывающих базы данных в составе проекта Azure Database Migration Service, который можно указать в качестве параметра для создания проекта. Для создания `AzDataMigrationDatabaseInfo`можно использовать командлет `New-AzDataMigrationDatabaseInfo`.

В следующем примере создается проект `AzDataMigrationDatabaseInfo` для базы данных **AdventureWorks2016** и добавляется в список, который предоставляется в качестве параметра для создания проекта.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Создание объекта проекта

Наконец, можно создать Azure Database Migration Service проект с именем *мидмспрожект* , расположенный в *восточной части США* , с помощью `New-AzDataMigrationProject` и добавить ранее созданное исходное и целевое подключения и список баз данных для миграции.

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

Затем создайте и запустите задачу Azure Database Migration Service. Для этой задачи требуются учетные данные подключения как для источника, так и для целевого объекта, а также список таблиц базы данных для переноса и сведения, уже предоставленные вместе с проектом, созданным в качестве необходимого компонента.

### <a name="create-credential-parameters-for-source-and-target"></a>Создание параметров учетных данных для исходного и целевого подключения

Создайте учетные данные безопасности подключения в виде объекта [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) .

В следующем примере показано создание объектов *PSCredential* как для исходных, так и для целевых подключений, предоставляя пароли как строковые переменные *$sourcePassword* и *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Создание объекта общей папки для архивации

Теперь создайте объект общего ресурса, представляющий локальную сетевую папку SMB, в которую Azure Database Migration Service может принимать резервные копии базы данных-источника с помощью командлета `New-AzDmsFileShare`.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Создание объекта выбранной базы данных

Следующим шагом является выбор исходной и целевой баз данных с помощью командлета `New-AzDmsSelectedDB`.

Следующий пример предназначен для миграции одной базы данных из SQL Server в управляемый экземпляр базы данных SQL Azure:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Если в управляемый экземпляр базы данных SQL Azure требуется выполнить перетаскивание по всему экземпляру SQL Server, то ниже будет цикл, чтобы получить все базы данных из источника. В следующем примере для $Server, $SourceUserName и $SourcePassword укажите сведения об источнике SQL Server.

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

Существует несколько дополнительных требований, которые необходимо решить, но они отличаются в зависимости от того, выполняется ли миграция в автономный или сетевой режим.

#### <a name="offline-migrations"></a>Автономные миграции

Для автономных миграций выполните следующие дополнительные задачи настройки.

* **Выберите имена входа**. Создайте список имен входа для переноса, как показано в следующем примере:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > В настоящее время Azure Database Migration Service поддерживает только миграцию имен входа SQL.

* **Выберите задания агента**. Создайте список заданий агента для переноса, как показано в следующем примере:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > В настоящее время Azure Database Migration Service поддерживает только задания с шагами заданий подсистемы T-SQL.

#### <a name="online-migrations"></a>Оперативные миграции

Для оперативной миграции выполните следующие дополнительные задачи настройки.

* **Настройка приложения Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Настройка ресурса хранилища**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Создание и запуск задачи миграции

Выполните командлет `New-AzDataMigrationTask`, чтобы создать и запустить задачу миграции.

#### <a name="specify-parameters"></a>Указание параметров

##### <a name="common-parameters"></a>Общие параметры

Независимо от того, выполняется ли миграция вне сети или в режиме «в сети», командлет `New-AzDataMigrationTask` предпринимает следующие параметры:

* *TaskType.* Для создаваемой задачи миграции для типа миграции "из SQL Server в Управляемый экземпляр Базы данных SQL Azure" ожидается тип *MigrateSqlServerSqlDbMi*. 
* *ResourceGroupName.* Имя группы ресурсов Azure, в которой будет создана задача.
* *ServiceName.* Экземпляр Azure Database Migration Service, в котором будет создана задача.
* *ProjectName.* Имя проекта Azure Database Migration Service, в котором будет создана задача. 
* *TaskName.* Имя задачи, которая будет создана. 
* *SourceConnection*. Объект Аздмсконнинфо, представляющий соединение с источником SQL Server.
* *TargetConnection.* Объект Аздмсконнинфо, представляющий целевое Управляемый экземпляр Базы данных SQL Azure соединение.
* *SourceCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к исходному серверу.
* *TargetCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к целевому серверу.
* *SelectedDatabase.* Объект Аздатамигратионселектеддб, представляющий сопоставление исходного и целевого базы данных.
* *BackupFileShare.* Объект FileShare, представляющий локальную сетевую папку, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника.
* *BackupBlobSasUri.* URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий. Узнайте, как получить URI SAS для контейнера больших двоичных объектов.
* *SelectedLogins.* Список выбранных для переноса имен для входа.
* *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

##### <a name="additional-parameters"></a>Дополнительные параметры

Командлет `New-AzDataMigrationTask` также предполагает, что параметры, уникальные для типа миграции, в автономном или оперативном режиме, выполняются.

* **Автономные миграции**. Для автономных миграций командлет `New-AzDataMigrationTask` также ожидает следующие параметры:

  * *SelectedLogins.* Список выбранных для переноса имен для входа.
  * *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

* **Оперативная миграция**. Для оперативной миграции командлет `New-AzDataMigrationTask` также ожидает следующие параметры.

* *Азуреактиведиректоряпп*. Active Directory приложение.
* *Сторажересаурцеид*. Идентификатор ресурса учетной записи хранения.

#### <a name="create-and-start-an-offline-migration-task"></a>Создание и запуск задачи автономной миграции

В следующем примере создается и запускается задача автономной миграции с именем **мидмстаск**:

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

#### <a name="create-and-start-an-online-migration-task"></a>Создание и запуск задачи оперативной миграции

В следующем примере создается и запускается задача оперативной миграции с именем **мидмстаск**:

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

Чтобы отслеживать миграцию, выполните следующие задачи.

1. Объедините все сведения о миграции в переменную с именем $CheckTask.

    Чтобы объединить сведения о миграции, такие как свойства, состояние и сведения о базе данных, связанные с миграцией, используйте следующий фрагмент кода:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Используйте переменную `$CheckTask`, чтобы получить текущее состояние задачи миграции.

    Чтобы использовать переменную `$CheckTask` для получения текущего состояния задачи миграции, можно отслеживать выполнение задачи миграции, запросив свойство State задачи, как показано в следующем примере:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Выполнение прямую миграцию (только миграция в сети)

При оперативной миграции выполняется полное резервное копирование и восстановление баз данных, после чего работа продолжается при восстановлении журналов транзакций, хранящихся в Баккупфилешаре.

Когда база данных в управляемом экземпляре базы данных SQL Azure обновляется последними данными и синхронизируется с базой данных-источником, можно выполнить прямую миграцию.

В следующем примере выполняется завершение кутовер\мигратион. Пользователи вызывают эту команду по собственному усмотрению.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Удаление экземпляра Azure Database Migration Service

После завершения миграции можно удалить экземпляр Azure Database Migration Service.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о дополнительных сценариях миграции (пары "источник — Целевая версия") см. в разделе [руководств по миграции баз данных](https://datamigration.microsoft.com/)Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о Azure Database Migration Service в статье [что такое Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
