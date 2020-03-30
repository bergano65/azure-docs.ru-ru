---
title: "PowerShell: Перенос сервера S'L в управляемый экземпляр S'L"
titleSuffix: Azure Database Migration Service
description: Научитесь переходить с внутреннего сервера S'L на экземпляр базы данных Azure S'L, управляемый экземпляром с помощью Azure PowerShell и службы миграции баз данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650730"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Переносите сервер S'L в управляемую базу данных СЗЛ с помощью службы миграции базы данных PowerShell & Azure

В этой статье вы переносите базу данных **Adventureworks2016,** восстановленную в предприимческое экземпляр S'L Server 2005 или выше, в экземпляр базы данных Azure S'L, управляемую экземпляром, используя Microsoft Azure PowerShell. Можно перенести базы данных из внутреннего экземпляра сервера S'L в экземпляр `Az.DataMigration` базы данных Azure S'L, управляемый экземпляром, используя модуль в Microsoft Azure PowerShell.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * Создайте группу ресурсов.
> * создание экземпляра Azure Database Migration Service;
> * Создайте проект миграции в экземпляре миграционной службы базы данных Azure.
> * выполнение миграции.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Эта статья содержит подробную информацию о том, как выполнять как онлайн, так и офлайн-миграцию.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этих действий вам потребуется следующее:

* [Сервер S'L 2016 или выше](https://www.microsoft.com/sql-server/sql-server-downloads) (любое издание).
* Локальная копия базы данных **AdventureWorks2016,** которая доступна для скачивания [здесь](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* При установке SQL Server Express включите протокол TCP/IP, который отключен по умолчанию. Для этого выполните инструкции в разделе [Использование диспетчера конфигурации SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) статьи "Включение или отключение сетевого протокола сервера".
* [Настройте брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Подписка Azure. Если у вас его нет, [создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) прежде чем начать.
* Управляемый экземпляром базы данных Azure S'L. Можно создать экземпляр управляемой базы данных Azure S'L, следуя подробной информации в статье [Создание управляемого экземпляра базы данных Azure S'L.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)
* Загрузить и установить [помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 или позже.
* Виртуальная сеть Microsoft Azure, созданная с использованием модели развертывания ресурсов Azure Manager, которая обеспечивает миграционную службу баз данных Azure с подключением к исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Завершенная оценка вашей базы данных и миграции схем с помощью помощника по миграции данных, как описано в статье [Выполнение оценки миграции сервера S'L](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)Server.
* Загрузить и установить `Az.DataMigration` модуль (версия 0.7.2 или более позднего) из галереи PowerShell с помощью [Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Для обеспечения того, чтобы учетные данные, используемые для подключения к исходной экземпляру сервера, имели разрешение [CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Для обеспечения того, чтобы учетные данные, используемые для подключения к целевой базе данных Azure S'L, использовали экземпляр базы данных, управляемый системой данных, управляемый системой данных azure S'L Database.

    > [!IMPORTANT]
    > Для миграций в Интернете необходимо настроить учетные данные Active Directory Azure. Для получения дополнительной информации см. статью [Используйте портал для создания приложения Azure AD и принципа службы, которое может получить доступ к ресурсам.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Подпишитесь на подписку Microsoft Azure

Подпишитесь на подписку Azure с помощью PowerShell. Для получения дополнительной информации смотрите статью [Войти в с Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) с помощью команды.

Следующий пример создает ресурсную группу под названием *myResourceGroup* в восточном регионе *США.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service

Вы можете создать экземпляр Azure Database Migration Service, выполнив командлет `New-AzDataMigrationService`.
Для этого командлета нужно передать следующие обязательные параметры:

* *Название группы ресурсов Azure*. Вы можете [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) использовать команду для создания группы ресурсов Azure, как показано ранее, и предоставить ее имя в качестве параметра.
* *Имя службы*. Строка, соответствующая желаемому уникальному названию службы данных Azure Database.
* *Расположение*. Указывает расположение службы. Укажите местоположение центра обработки данных Azure, например, в Западной части США или в юго-восточной Азии.
* *Sku*. Этот параметр соответствует имени SKU DMS. В настоящее время поддерживаемые имена Sku *Basic_1vCore,* *Basic_2vCores,* *GeneralPurpose_4vCores*.
* *VirtualSubnetId.* Вы можете использовать cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) для создания подсети.

Следующий пример создает сервис под названием *MyDMS* в ресурсной группе *MyDMSResourceGroup,* расположенной в восточном регионе *США,* используя виртуальную сеть под названием *MyVNET* и подсеть под названием *MySubnet*.

> [!IMPORTANT]
> Фрагмент кода ниже предназначен для автономной миграции, которая не требует экземпляра миграционной службы базы данных Azure на основе Premium SKU. Для онлайн-миграции значение параметра -Sku должно включать Премиум SKU.

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

Объект Database Connection Info можно создать `New-AzDmsConnInfo` с помощью cmdlet, который ожидает следующие параметры:

* *ServerType*. Тип запрошенного подключения к базе данных, например SQL, Oracle или MySQL. Используйте SQL для SQL Server и SQL Azure.
* *DataSource*. Имя или IP экземпляра сервера S'L или экземпляра базы данных Azure S'L.
* *AuthType*. Тип проверки подлинности для подключения. Это может быть SqlAuthentication или WindowsAuthentication.
* *TrustServerCertificate*. Этот параметр устанавливает значение, которое указывает, является ли канал зашифрован при обходе цепочки сертификатов для проверки доверия. Значение может `$true` быть `$false`или .

Следующий пример создает объект «Информация о подключении» для исходного сервера, называемого *MySourceSSLServer* с помощью проверки подлинности sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Следующий пример показывает создание информации о подключении для сервера управляемых экземпляров базы данных Azure s'L под названием "targetmanagedinstance.database.windows.net" с использованием проверки подлинности sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Указание баз данных для проекта миграции

Создайте список объектов, `AzDataMigrationDatabaseInfo` которые определяют базы данных в рамках проекта миграционной службы лазурных данных, который может быть предоставлен в качестве параметра для создания проекта. Вы можете использовать `New-AzDataMigrationDatabaseInfo` cmdlet `AzDataMigrationDatabaseInfo`для создания.

Следующий пример `AzDataMigrationDatabaseInfo` создает проект для базы данных **AdventureWorks2016** и добавляет его в список, который будет предоставлен в качестве параметра для создания проекта.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Создание объекта проекта

Наконец, можно создать проект службы миграции базы данных Azure под названием *MyDMSProject,* расположенный в *Восточной части США,* с помощью `New-AzDataMigrationProject` ранее созданных исходных и целевых соединений и списка баз данных для миграции.

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

Затем создайте и запустите задачу миграционной службы базы данных Azure. Эта задача требует информации о учетных данных соединения как для источника, так и для цели, а также для переноса списка таблиц баз данных и информации, уже предоставленной проектом, в качестве предварительного условия.

### <a name="create-credential-parameters-for-source-and-target"></a>Создание параметров учетных данных для исходного и целевого подключения

Создавайте учетные данные безопасности соединения как объект [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

В следующем примере показано создание объектов *PSCredential* как для исходных, так и для целевых соединений, предоставляя пароли в виде переменных строк *$sourcePassword* и *$targetPassword.*

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Создание объекта резервного копирования FileShare

Теперь создайте объект FileShare, представляющий локальную сеть SMB, к которой миграционная `New-AzDmsFileShare` служба базы данных Azure может взять резервные данные исходной базы с помощью cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Создание объекта выбранной базы данных

Следующим шагом является выбор исходных и целевых `New-AzDmsSelectedDB` баз данных с помощью cmdlet.

Следующим примером является перенос единой базы данных из сервера S'L в управляемый экземпляр базы данных Azure S'L:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Если всему экземпляру S'L Server требуется подъем и сдвиг в управляемый экземпляр базы данных Azure S'L, ниже предоставляется цикл, чтобы изымать все базы данных из источника. В следующем примере, для $Server, $SourceUserName и $SourcePassword, предоставьте информацию об исходном сервере S'L Server.

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

Есть несколько дополнительных требований, которые необходимо решить, но они различаются в зависимости от того, выполняете ли вы в автономном режиме или онлайн-миграции.

#### <a name="offline-migrations"></a>Офлайн-миграция

Только для автономных миграций выполните следующие дополнительные задачи конфигурации.

* **Выберите логины**. Создайте список логинов для миграции, как показано в следующем примере:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > В настоящее время миграционная служба базы данных Azure поддерживает только миграционные логины S'L.

* **Выберите задания агента.** Создайте список заданий агента, которые будут перенесены, как показано в следующем примере:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > В настоящее время служба миграции баз данных Azure поддерживает только задания с помощью заданий подсистемы T-S'L.

#### <a name="online-migrations"></a>Онлайн миграции

Только для миграций в Интернете выполните следующие дополнительные задачи конфигурации.

* **Настройка приложения Active Directory Azure**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Настройка ресурса для хранения**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Создание и запуск задачи миграции

Выполните командлет `New-AzDataMigrationTask`, чтобы создать и запустить задачу миграции.

#### <a name="specify-parameters"></a>Указание параметров

##### <a name="common-parameters"></a>Общие параметры

Независимо от того, выполняете ли вы офлайн-миграцию или онлайн-миграцию, `New-AzDataMigrationTask` cmdlet ожидает следующие параметры:

* *TaskType.* Для создаваемой задачи миграции для типа миграции "из SQL Server в Управляемый экземпляр Базы данных SQL Azure" ожидается тип *MigrateSqlServerSqlDbMi*. 
* *Название группы ресурсов*. Имя группы ресурсов Azure, в которой будет создана задача.
* *ServiceName*. Экземпляр Azure Database Migration Service, в котором будет создана задача.
* *ПроектИмя*. Имя проекта Azure Database Migration Service, в котором будет создана задача. 
* *TaskName*. Имя задачи, которая будет создана. 
* *SourceConnection*. Объект AzDmsConnInfo, представляющий исходное соединение сервера S'L.
* *TargetConnection.* Объект AzDmsConnInfo, представляющий целевое подключение управляемых экземпляров базы данных Azure S'L.
* *SourceCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к исходному серверу.
* *TargetCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к целевому серверу.
* *SelectedDatabase.* AzDataMigrationSelectedDB объект, представляющий исходное и целевое картографическое базирование.
* *BackupFileShare.* Объект FileShare, представляющий локальную сетевую папку, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника.
* *BackupBlobSasUri.* URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий. Узнайте, как получить URI SAS для контейнера больших двоичных объектов.
* *SelectedLogins.* Список выбранных для переноса имен для входа.
* *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

##### <a name="additional-parameters"></a>Дополнительные параметры

Cmdlet `New-AzDataMigrationTask` также ожидает параметры, которые являются уникальными для типа миграции, в автономном режиме или в Интернете, что вы выполняете.

* **Офлайн-миграция**. Для автономных миграций `New-AzDataMigrationTask` cmdlet также ожидает следующие параметры:

  * *SelectedLogins.* Список выбранных для переноса имен для входа.
  * *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

* **Онлайн миграции**. Для миграций в `New-AzDataMigrationTask` Интернете, cmdlet также ожидает следующие параметры.

* *AzureActiveDirectoryApp*. Активное приложение каталога.
* *ХранениеРесурсID*. Идентификатор ресурса учетной записи хранилища.

#### <a name="create-and-start-an-offline-migration-task"></a>Создание и запуск задачи миграции в автономном режиме

Следующий пример создает и запускает задачу переносной миграции под названием **myDMSTask:**

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

#### <a name="create-and-start-an-online-migration-task"></a>Создание и запуск задачи онлайн-миграции

Следующий пример создает и запускает задачу онлайн-миграции под названием **myDMSTask:**

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

Чтобы контролировать миграцию, выполните следующие задачи.

1. Консолидируйте все детали миграции в переменную, называемую $CheckTask.

    Для объединения сведений о миграции, таких как сведения о свойствах, состояниях и базе данных, связанные с миграцией, используйте следующий фрагмент кода:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Используйте `$CheckTask` переменную, чтобы получить текущее состояние задачи миграции.

    Чтобы использовать `$CheckTask` переменную для получения текущего состояния задачи миграции, можно проконтролировать задачу миграции, выполняемую, задав запрос на свойство состояния задачи, как показано в следующем примере:

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

## <a name="performing-the-cutover-online-migrations-only"></a>Выполнение сокращения (только онлайн-миграции)

При онлайн-миграции выполняется полное резервное копирование и восстановление баз данных, а затем продолжается работа по восстановлению журналов транзакций, хранящихся в BackupFileShare.

Когда база данных в управляемом экземпляре базы данных Azure S'L обновляется с последними данными и синхронизируется с исходной базой данных, можно выполнить пересчет.

Следующий пример завершит сокращение миграции. Пользователи ссылаться на эту команду по своему усмотрению.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Удаляем экземпляр миграционной службы баз данных Azure

После завершения миграции можно удалить экземпляр службы миграции базы данных Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения информации о дополнительных сценариях миграции [Database Migration Guide](https://datamigration.microsoft.com/)(исходные/целевые пары) см.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о миграционном обслуживании базы данных Azure читайте в материале [«Что такое миграционная служба баз данных Azure?».](https://docs.microsoft.com/azure/dms/dms-overview)
