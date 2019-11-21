---
title: Back up and restore Azure Files with PowerShell
description: In this article, learn how to back up and restore Azure Files using the Azure Backup service and PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 78000bc669eb7a61f8698ad8c39ef49f65b245a2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224156"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Back up and restore Azure Files with PowerShell

This article describes how to use Azure PowerShell to back up and recover an Azure Files file share using an [Azure Backup](backup-overview.md) Recovery Services vault.

В этой статье описано, как:

> [!div class="checklist"]
>
> * Set up PowerShell and register the Azure Recovery Services Provider.
> * Создайте хранилище служб восстановления.
> * Configure backup for an Azure file share.
> * Run a backup job.
> * Restore a backed up Azure file share, or an individual file from a share.
> * Monitor backup and restore jobs.

## <a name="before-you-start"></a>Перед началом работы

* [Learn more](backup-azure-recovery-services-vault-overview.md) about Recovery Services vaults.
* Read about the preview capabilities for [backing up Azure file shares](backup-azure-files.md).
* Review the PowerShell object hierarchy for Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

The object hierarchy is summarized in the following diagram.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Review the **Az.RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

## <a name="set-up-and-install"></a>Set up and install

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Set up PowerShell as follows:

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.0.0.

2. Find the Azure Backup PowerShell cmdlets with this command:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Review the aliases and cmdlets for Azure Backup, Azure Site Recovery, and the Recovery Services vault appear. Here's an example of what you might see. It's not a complete list of cmdlets.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Sign in to your Azure account with **Connect-AzAccount**.
5. On the web page that appears, you're prompted to input your account credentials.

    * Alternately, you can include your account credentials as a parameter in the **Connect-AzAccount** cmdlet with **-Credential**.
    * If you're a CSP partner working on behalf of a tenant, specify the customer as a tenant, using their tenantID or tenant primary domain name. Например: **Connect-AzAccount-Tenant** fabrikam.com.

6. Associate the subscription you want to use with the account, because an account can have several subscriptions.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик Служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verify that the providers registered successfully:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. In the command output, verify that **RegistrationState** changes to **Registered**. If it doesn't, run the **Register-AzResourceProvider** cmdlet again.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

1. A vault is placed in a resource group. If you don't have an existing resource group, create a new one with the [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In this example, we create a new resource group in the West US region.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Use the [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet to create the vault. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specify the type of redundancy to use for the vault storage.

   * [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md).
   * The following example sets the **-BackupStorageRedundancy** option for the[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd for **testvault** set to **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

The output is similar to the following. Note that the associated resource group and location are provided.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Задание контекста хранилища

Store the vault object in a variable, and set the vault context.

* Many Azure Backup cmdlets require the Recovery Services vault object as an input, so it's convenient to store the vault object in a variable.
* Контекст хранилища — это тип данных, защищаемых в хранилище. Set it with [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). After the context is set, it applies to all subsequent cmdlets.

В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Fetch the vault ID

We plan on deprecating the vault context setting in accordance with Azure PowerShell guidelines. Instead, you can store or fetch the vault ID, and pass it to relevant commands. So, if you haven't set the vault context or want to specify the command to run for a certain vault, pass the vault ID as "-vaultID" to all relevant command as follows:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

A backup policy specifies the schedule for backups, and how long backup recovery points should be kept:

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* View the default backup policy retention using [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* View the default backup policy schedule using [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* You use the [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet to create a new backup policy. You input the schedule and retention policy objects.

By default, a start time is defined in the Schedule Policy Object. Use the following example to change the start time to the desired start time. The desired start time should be in UTC as well. The below example assumes the desired start time is 01:00 AM UTC for daily backups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> You need to provide the start time in 30 minute multiples only. In the above example, it can be only "01:00:00" or "02:30:00". The start time cannot be "01:15:00"

В следующем примере показано сохранение политик расписания и хранения в переменных. It then uses those variables as parameters for a new policy (**NewAFSPolicy**). **NewAFSPolicy** выполняет ежедневное резервное копирование и хранит его в течение 30 дней.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

The output is similar to the following.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Включение резервного копирования

After you define the backup policy, you can enable the protection for the Azure file share using the policy.

### <a name="retrieve-a-backup-policy"></a>Retrieve a backup policy

You fetch the relevant policy object with [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use this cmdlet to get a specific policy, or to view the policies associated with a workload type.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Retrieve a policy for a workload type

The following example retrieves policies for the workload type **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

The output is similar to the following.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Часовой пояс поля **BackupTime** в PowerShell — время в формате UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.

### <a name="retrieve-a-specific-policy"></a>Retrieve a specific policy

Следующая политика извлекает политику резервного копирования с именем **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Enable backup and apply policy

Enable protection with [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). After the policy is associated with the vault, backups are triggered in accordance with the policy schedule.

The following example enables protection for the Azure file share **testAzureFileShare** in storage account **testStorageAcct**, with the policy **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Команда ожидает завершения задания настройки защиты и предоставляет результаты, подобные приведенным ниже.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Активирование резервного копирования по запросу

Use [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) to run an on-demand backup for a protected Azure file share.

1. Retrieve the storage account and file share from the container in the vault that holds your backup data with [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Чтобы запустить задание резервного копирования, получите сведения о виртуальной машине с помощью командлета [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Run an on-demand backup with[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Run the on-demand backup as follows:

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

### <a name="using-on-demand-backups-to-extend-retention"></a>Using on-demand backups to extend retention

On-demand backups can be used to retain your snapshots for 10 years. Schedulers can be used to run on-demand PowerShell scripts with chosen retention and thus take snapshots at regular intervals every week, month, or year. While taking regular snapshots, refer to the [limitations of on-demand backups](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) using Azure backup.

If you are looking for sample scripts, you can refer to the sample script on GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) using Azure Automation runbook that enables you to schedule backups on a periodic basis and retain them even up to 10 years.

### <a name="modify-the-protection-policy"></a>Изменение политики защиты

To change the policy used for backing up the Azure file share, use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Specify the relevant backup item and the new backup policy.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Restore Azure file shares and files

You can restore an entire file share or specific files on the share. You can restore to the original location, or to an alternate location.

### <a name="fetch-recovery-points"></a>Получение точек восстановления

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) to list all recovery points for the backed-up item.

In the following script:

* The variable **$rp** is an array of recovery points for the selected backup item from the past seven days.
* Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**.
* Используйте стандартное индексирование массива PowerShell для выбора точки восстановления.
* В примере **$rp[0]** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

The output is similar to the following.

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

After the relevant recovery point is selected, you restore the file share or file to the original location, or to an alternate location.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restore an Azure file share to an alternate location

Use the [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) to restore to the selected recovery point. Specify these parameters to identify the alternate location:

* **TargetStorageAccountName**: The storage account to which the backed-up content is restored. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**: The file shares within the target storage account to which the backed-up content is restored.
* **TargetFolder**: The folder under the file share to which data is restored. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **ResolveConflict**: Instruction if there's a conflict with the restored data. Принимает **перезаписать** или **пропустить**.

Run the cmdlet with the parameters as follows:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Restore an Azure file to an alternate location

Use the [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) to restore to the selected recovery point. Specify these parameters to identify the alternate location, and to uniquely identify the file you want to restore.

* **TargetStorageAccountName**: The storage account to which the backed-up content is restored. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**: The file shares within the target storage account to which the backed-up content is restored.
* **TargetFolder**: The folder under the file share to which data is restored. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**: The absolute path of the file, to be restored within the file share, as a string. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **SourceFileType**: Whether a directory or a file is selected. Принимает **папка** или **файл**.
* **ResolveConflict**: Instruction if there's a conflict with the restored data. Принимает **перезаписать** или **пропустить**.

The additional parameters (SourceFilePath and SourceFileType) are related only to the individual file you want to restore.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

This command returns a job with an ID to be tracked, as shown in the previous section.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restore Azure file shares and files to the original location

When you restore to an original location, you don't need to specify destination- and target-related parameters. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

On-demand backup and restore operations return a job along with an ID, as shown when you [ran an on-demand backup](#trigger-an-on-demand-backup). Use the [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet to track the job progress and details.

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

## <a name="next-steps"></a>Дальнейшие действия

[Learn about](backup-azure-files.md) backing up Azure Files in the Azure portal.
