---
title: Резервное копирование и восстановление файлов Azure с помощью службы архивации Azure и PowerShell
description: Резервное копирование и восстановление файлов Azure с помощью службы архивации Azure и PowerShell.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 83fe8d17699c19d442fd734d71d828eb9fd9d6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258366"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Резервное копирование и восстановление файлов Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования и восстановления файлов Azure файловый ресурс с помощью [Azure Backup](backup-overview.md) хранилище служб восстановления. 

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
> * Настроить PowerShell и зарегистрировать поставщик служб восстановления Azure.
> * Создайте хранилище служб восстановления,
> * Настройка резервного копирования для файлового ресурса Azure.
> * Запустите задание резервного копирования.
> * Восстановление резервной копии файлового ресурса Azure или отдельный файл из общей папки.
> * Мониторинг резервного копирования и восстановления заданий.


## <a name="before-you-start"></a>Перед началом работы

- [Дополнительные сведения](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
- Ознакомьтесь с возможностями предварительной версии для [резервное копирование файловых ресурсов Azure](backup-azure-files.md).
- Просмотр иерархии объектов PowerShell для служб восстановления.


## <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Объект иерархию на следующей схеме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Просмотрите **Az.RecoveryServices** [Справочник по командлетам](/powershell/module/az.recoveryservices) ссылку в библиотеке Azure.


## <a name="set-up-and-install"></a>Настройка и установка

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Настройте PowerShell следующим образом:

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.0.0.

2. Найти командлеты Azure Backup PowerShell с помощью следующей команды:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Просмотрите псевдонимов и командлетов Azure Site Recovery и хранилище служб восстановления для службы Azure Backup. Вот что можно увидеть пример. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Войдите в учетную запись Azure с помощью **Connect AzAccount**.
4. На появившейся веб-странице будет предложено ввести учетные данные учетной записи.

    - Кроме того, можно добавить учетные данные учетной записи в качестве параметра **Connect AzAccount** командлет с **-Credential**.
    - Если вы являетесь партнером CSP, работающий от имени клиента, укажите заказчика в качестве клиента, с помощью их основное доменное имя клиента или идентификатор клиента. Например: **Connect-AzAccount-Tenant** fabrikam.com.

4. Свяжите подписку, которую вы хотите использовать с учетной записью, так как учетной записи может быть несколько подписок.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик Служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Убедитесь, что поставщики успешно зарегистрированы:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Убедитесь, что в результатах выполнения команды **RegistrationState** примет **зарегистрированные**. Если он не запускается **Register AzResourceProvider** командлет еще раз.



## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

- Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение. 

1. Хранилище будет помещено в группу ресурсов. Если у вас нет существующей группы ресурсов, создайте новую с [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). В этом примере мы создадим новую группу ресурсов в западной части США.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Используйте [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) командлет, чтобы создать хранилище. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Укажите тип избыточности для хранилища хранилище.

   - [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md).
   - В следующем примере задается **- BackupStorageRedundancy** для параметра[набора AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd для **testvault** присвоено  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выходные данные следующего вида. Обратите внимание на то, что предоставлены связанная группа ресурсов и расположение.

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

Store объект хранилища в переменной и задайте контекст хранилища.

- Многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входного, поэтому его можно хранить объект хранилища в переменной.
- Контекст хранилища — это тип данных, защищаемых в хранилище. Установка его с помощью [AzRecoveryServicesVaultContext набора](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Контекст равен, он применяется ко всем последующим командлетам.


В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Получить идентификатор хранилища

Мы планируем перевод в разряд нерекомендуемых контекст хранилища, установка в соответствии с рекомендациями Azure PowerShell. Вместо этого можно сохранить или получить идентификатор хранилища, а передайте соответствующие команды следующим образом:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

Политика архивации указывает расписание для резервного копирования и срок хранения точек восстановления:

- Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
- Представление хранения политику резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Представление расписания политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Использовании [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) командлет, чтобы создать новую политику резервного копирования. Введенные объекты политик расписания и хранения.

В следующем примере показано сохранение политик расписания и хранения в переменных. Затем используется этим переменной как параметры для новой политики (**NewAFSPolicy**). **NewAFSPolicy** выполняет ежедневное резервное копирование и хранит его в течение 30 дней.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выходные данные следующего вида.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Включение резервного копирования

После определения политики резервного копирования, вы можете включить защиту для файлового ресурса Azure с помощью политики.

### <a name="retrieve-a-backup-policy"></a>Получить политику резервного копирования

Получить объект соответствующие политики с [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Чтобы получить конкретную политику, или просмотреть политики, связанные с типом рабочей нагрузки с помощью этого командлета.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Получить политику для тип рабочей нагрузки

В следующем примере извлекается политики для тип рабочей нагрузки **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Выходные данные следующего вида.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Часовой пояс поля **BackupTime** в PowerShell — время в формате UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.

### <a name="retrieve-a-specific-policy"></a>Получить конкретную политику

Следующая политика извлекает политику резервного копирования с именем **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Включить резервное копирование и применение политики

Включение защиты с помощью [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). После того как политика связана с хранилищем, резервные копии будут создаваться в соответствии с расписанием политики.

В следующем примере включается защита для файлового ресурса Azure **testAzureFileShare** в учетной записи хранения **testStorageAcct**, с помощью политики **dailyafs**.

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

Используйте [AzRecoveryServicesBackupItem резервного копирования](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) чтобы выполнять резервное копирование по запросу для защищенных файлового ресурса Azure.

1. Получение учетной записи хранения и файловый ресурс из контейнера в хранилище, которое содержит данные резервного копирования с [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Чтобы запустить задание резервного копирования, необходимо получить сведения о виртуальной Машине с [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Выполнять резервное копирование по запросу с[AzRecoveryServicesBackupItem резервного копирования](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Выполните резервное копирование по требованию следующим образом:
    
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

Чтобы изменить политику, используемая для резервного копирования файлового ресурса Azure, используйте [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Укажите соответствующий элемент резервного копирования, а новая политика резервного копирования.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Восстановление файловых ресурсов Azure и файлов

Вы можете восстановить файловый ресурс или отдельные файлы в общей папке. Можно восстановить в исходное расположение или в альтернативное расположение. 

### <a name="fetch-recovery-points"></a>Получение точек восстановления

Используйте [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) Чтобы получить список всех точек восстановления для резервных копий элемента.

В следующем сценарии:

- Переменная **$rp** представляет собой массив точек восстановления для выбранного архивного элемента за последние семь дней.
- Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**.
- Используйте стандартное индексирование массива PowerShell для выбора точки восстановления.
- В примере **$rp[0]** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Выходные данные следующего вида.

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
После выбора точки восстановления, соответствующих вам восстановить общую папку или файл в исходное расположение или в альтернативное расположение.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Восстановить файловый ресурс Azure в альтернативное расположение

Используйте [AzRecoveryServicesBackupItem восстановления](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) восстановить до выбранной точки восстановления. Укажите эти параметры для определения альтернативного расположения: 

- **TargetStorageAccountName**. Учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
- **TargetFileShareName**. Файловые ресурсы находятся в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
- **TargetFolder**. Папка в файловом ресурсе, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
- **ResolveConflict**. Инструкции в случае конфликта с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Выполните командлет с параметрами следующим образом:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Восстановление в альтернативное расположение файла Azure

Используйте [AzRecoveryServicesBackupItem восстановления](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) восстановить до выбранной точки восстановления. Указать эти параметры для определения альтернативного расположения, а для однозначной идентификации файла требуется выполнить восстановление.

* **TargetStorageAccountName**. Учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**. Файловые ресурсы находятся в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**. Папка в файловом ресурсе, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**. Абсолютный путь к файлу, который будет восстановлен в общей папке, в виде строки. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **SourceFileType**. Выбирается или каталог, или файл. Принимает **папка** или **файл**.
* **ResolveConflict**. Инструкции в случае конфликта с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Дополнительные параметры (SourceFilePath и SourceFileType) относятся только к отдельного файла, который вы хотите восстановить.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Эта команда возвращает задания с Идентификатором, которые нужно отслеживать, как показано в предыдущем разделе.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Восстановление файловых ресурсов Azure и файлы в исходное расположение

При восстановлении в исходное расположение, не требуется для указания параметров, связанных с назначения и целевой объект. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

Операции резервного копирования и восстановления по запросу возвращать задание вместе с Идентификатором, как вы [выполнили резервное копирование по запросу](#trigger-an-on-demand-backup). Используйте [Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) командлет, чтобы отслеживать ход выполнения задания и сведения.

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
[Дополнительные сведения о](backup-azure-files.md) резервном копировании файлов Azure на портале Azure.
