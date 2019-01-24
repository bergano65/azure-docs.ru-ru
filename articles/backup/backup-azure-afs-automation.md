---
title: Развертывание резервных копий для файловых ресурсов Azure с помощью PowerShell и управление ими
description: Использование PowerShell для развертывания и управления резервными копиями в Azure для файловых ресурсов
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell; Резервное копирование файлов Azure; Восстановление файлов Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: bbeccd03fffb699c95d52d50ec5c45e38b43ef51
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430404"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Использование PowerShell для резервного копирования и восстановления файловых ресурсов Azure

В этой статье показано, как выполнять архивацию и восстановление файловых ресурсов Azure из хранилища служб восстановления с помощью командлетов Azure PowerShell. Хранилище Служб восстановления — это ресурс Azure Resource Manager, используемый для защиты данных и ресурсов-контейнеров в службе архивации Azure и службах Azure Site Recovery.

## <a name="concepts"></a>Основные понятия

Если вы не знакомы со службой архивации Azure, общие сведения о службе см. в [этой](backup-introduction-to-azure-backup.md) статье. Перед началом убедитесь, что вы приняли к сведению возможности предварительного просмотра резервных копий общих файловых ресурсов Azure, задокументированных [здесь](backup-azure-files.md).

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, с чего следует начать.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Справку по командлету PowerShell **AzureRm.RecoveryServices.Backup** см. в статье [Azure Backup — Recovery Services cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) (Командлеты служб архивации и восстановления Azure) в библиотеке Azure.

## <a name="setup-and-registration"></a>Настройка и регистрация

> [!NOTE]
> Как отмечено [здесь](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0), поддержка новых функций в модуле AzureRM заканчивается в ноябре 2018 года. Таким образом, мы предоставляем поддержку резервного копирования общих ресурсов файлов Azure с новым модулем PowerShell "Az" (сейчас в общедоступной версии).

Чтобы начать работу, выполните следующие действия.

1. [Скачайте последнюю версию Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). Минимальная требуемая версия — 1.0.0.

2. Чтобы получить список доступных командлетов **PowerShell для службы архивации Azure**, введите следующую команду.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Отображаются псевдонимы и командлеты для службы архивации Azure, Azure Site Recovery и хранилища служб восстановления. Ниже приведен пример того, что вы увидите. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Чтобы войти в учетную запись Azure, используйте командлет **Connect-AzAccount**. Откроется веб-страница, на которой пользователю предлагается ввести данные для входа в учетную запись.

    * Кроме того, учетные данные можно добавить в качестве параметра в командлет **Connect-AzAccount**, используя параметр **-Credential**.
    * Если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента. Для этого нужно ввести идентификатор или основное доменное имя клиента. Например: **Connect-AzAccount-Tenant** fabrikam.com.

4. Свяжите подписку, которую собираетесь использовать, с учетной записью, так как последняя может иметь несколько подписок.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик Служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Убедитесь, что поставщики успешно зарегистрированы, выполнив следующие команды.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    В выходных данных команды для **RegistrationState** должно быть установлено значение **Registered**. Если это не так, повторно выполните командлет **Register-AzResourceProvider**.

С помощью PowerShell можно автоматизировать следующие задачи:

* Создайте хранилище служб восстановления,
* настройка резервного копирования файловых ресурсов Azure;
* активация задания архивации;
* наблюдение за выполнением задания архивации;
* восстановление файлового ресурса Azure;
* восстановление отдельных файлов Azure из файлового ресурса Azure.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

1. Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Воспользуйтесь командлетом **New-AzRecoveryServicesVault**, чтобы создать хранилище Служб восстановления. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Укажите необходимый тип избыточности хранилища: [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md). В следующем примере показано, что для параметра **-BackupStorageRedundancy** для **testvault** задано значение **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет **Get-AzRecoveryServicesVault**, вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Результат будет похож на следующий пример. Обратите внимание, что указаны соответствующие **ResourceGroupName** и **Location**.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных.

Используйте командлет **Set-AzRecoveryServicesVaultContext**, чтобы задать контекст хранилища. Заданный контекст хранилища применяется ко всем последующим командлетам. В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Мы планируем отказаться от параметра контекста хранилища в соответствии с рекомендациями Azure PowerShell. Вместо этого мы рекомендуем пользователям передавать идентификатор хранилища, приведенный ниже.

В качестве альтернативы вы можете сохранить или извлечь идентификатор хранилища, для которого необходимо выполнить операцию PowerShell, и передать его в соответствующую команду.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Настройка резервного копирования файлового ресурса Azure

### <a name="create-a-protection-policy"></a>Создание политики защиты

Политика защиты архивации связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления. Для просмотра политики хранения по умолчанию используется командлет **Get-AzRecoveryServicesBackupRetentionPolicyObject**. 

Аналогичным образом для получения политики расписания по умолчанию используется командлет **Get-AzRecoveryServicesBackupSchedulePolicyObject**. Командлет **New-AzRecoveryServicesBackupProtectionPolicy** создает объект PowerShell, который содержит сведения о политике архивации. Объекты политик расписания и хранения используются в качестве входных данных в командлете **New-AzRecoveryServicesBackupProtectionPolicy**. 

В следующем примере показано сохранение политик расписания и хранения в переменных. В примере эти переменные используются для определения параметров при создании политики защиты **NewPolicy**.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Результат будет похож на следующий пример.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

**NewAFSPolicy** выполняет ежедневное резервное копирование и хранит его в течение 30 дней.

### <a name="enable-protection"></a>Включить защиту

Определив политику защиты, вы можете включить защиту для общего файлового ресурса Azure с помощью этой политики.

Получите соответствующий объект политики с помощью командлета **Get-AzRecoveryServicesBackupProtectionPolicy**. С его помощью можно получить конкретную политику или просмотреть политики, связанные с типом рабочей нагрузки.

В следующем примере возвращаются политики для типа рабочей нагрузки **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Результат будет похож на следующий пример.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Часовой пояс поля **BackupTime** в PowerShell — время в формате UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.
>
>

Следующая политика извлекает политику резервного копирования с именем **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Для включения защиты элемента с заданной политикой используйте **Enable AzRecoveryServicesBackupProtection**. После того как политика сопоставится с хранилищем, рабочий процесс архивации будет активироваться по времени, определенному в политике расписания.

В следующем примере включается защита для файлового ресурса Azure **testAzureFileShare** в разделе учетной записи хранения **testStorageAcct** с помощью политики **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Команда ожидает завершения задания настройки защиты и предоставляет результаты, подобные приведенным ниже.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Активирование резервного копирования по запросу

Для запуска задания резервного копирования для защищенного файлового ресурса Azure используйте **Backup-AzRecoveryServicesBackupItem**. Извлеките учетную запись хранения и общий файловый ресурс в ней, используя следующие команды, и запустите резервное копирование по требованию.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Мы используем моментальные снимки общего ресурса файлов Azure при создании резервных копий и поэтому задание, как правило, завершается к тому времени, когда команда возвращает выходные данные.

### <a name="modify-the-protection-policy"></a>Изменение политики защиты

Для изменения политики, с помощью которой защищен файловый ресурс Azure, используйте **Enable-AzRecoveryServicesBackupProtection** с соответствующим элементом резервного копирования и новую политику защиты.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Восстановление файлов и файловых ресурсов Azure

Вы можете целиком восстановить файловый ресурс в исходное или альтернативное расположение. Аналогичным образом можно также восстановить отдельные файлы из файлового ресурса.

### <a name="fetch-recovery-points"></a>Получение точек восстановления

Выполните командлет **Get-AzRecoveryServicesBackupRecoveryPoint**, чтобы получить полный список точек восстановления. В следующем сценарии переменная **$rp** представляет собой массив точек восстановления для выбранного архивного элемента за последние семь дней. Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. В примере **$rp[0]** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Результат будет похож на следующий пример.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

После того как соответствующая точка восстановления выбрана, приступайте к восстановлению файла или файлового ресурса в альтернативное или исходное расположение, как описано ниже.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Восстановление файловых ресурсов Azure в альтернативное расположение

#### <a name="restore-an-azure-file-share"></a>восстановление файлового ресурса Azure;

Определите альтернативное расположение, указав следующие сведения.

* **TargetStorageAccountName**. Учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**. Файловые ресурсы находятся в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**. Папка в файловом ресурсе, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **ResolveConflict**. Инструкции в случае конфликта с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Укажите эти параметры в команде восстановления, чтобы восстановить резервную копию файлового ресурса в другом месте.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restore-an-azure-file"></a>Восстановление файла Azure

Если вам необходимо выполнить восстановление отдельного файла вместо всего файлового ресурса, он должен быть однозначно идентифицирован по следующим параметрам.

* **TargetStorageAccountName**. Учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**. Файловые ресурсы находятся в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**. Папка в файловом ресурсе, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**. Абсолютный путь к файлу, который будет восстановлен в общей папке, в виде строки. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **SourceFileType**. Выбирается или каталог, или файл. Принимает **папка** или **файл**.
* **ResolveConflict**. Инструкции в случае конфликта с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Дополнительные параметры связаны только с отдельным файлом для восстановления.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Кроме того, эта команда возвращает задание для отслеживания с идентификатором, как показано выше.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Восстановление файловых ресурсов Azure в исходное расположение

В случае восстановления в исходное расположение все параметры, связанные с назначением и целью, указывать не обязательно. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

Операции резервного копирования и восстановления по запросу возвращают задание наряду с идентификатором, как показано [выше](#trigger-an-on-demand-backup). Используйте командлет **Get-AzRecoveryServicesBackupJobDetails**, чтобы отслеживать выполнение задания и получать дополнительные сведения.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
