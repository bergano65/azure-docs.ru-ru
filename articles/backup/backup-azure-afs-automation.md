---
title: Резервное копирование и восстановление файлов Azure с помощью Azure Backup и PowerShell
description: В этой статье вы узнаете, как выполнять резервное копирование и восстановление файлов Azure с помощью службы Azure Backup и PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dacurwin
ms.reviewer: pullabhk
ms.openlocfilehash: 4642549dcdd455c97ea3e8fd8a8b5440779b923c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903616"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Резервное копирование и восстановление файлов Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования и восстановления файлового ресурса службы файлов Azure с помощью хранилища служб восстановления [Azure Backup](backup-overview.md) .

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
>
> * Настройте PowerShell и зарегистрируйте поставщик служб восстановления Azure.
> * Создайте хранилище служб восстановления.
> * Настройте резервное копирование для файлового ресурса Azure.
> * Запустите задание резервного копирования.
> * Восстановление резервной копии общего файлового ресурса Azure или отдельного файла из общей папки.
> * Мониторинг заданий резервного копирования и восстановления.

## <a name="before-you-start"></a>Перед началом работы

* Дополнительные [сведения](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Ознакомьтесь с возможностями предварительной версии для [резервного копирования файловых ресурсов Azure](backup-azure-files.md).
* Проверьте иерархию объектов PowerShell для служб восстановления.

## <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Иерархия объектов представлена на следующей схеме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Ознакомьтесь со **справочником по командлету** [AZ. RecoveryServices](/powershell/module/az.recoveryservices) в библиотеке Azure.

## <a name="set-up-and-install"></a>Установка и установка

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Настройте PowerShell следующим образом.

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.0.0.

2. Найдите командлеты PowerShell Azure Backup с помощью следующей команды:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Проверьте псевдонимы и командлеты для Azure Backup, Azure Site Recovery и хранилища служб восстановления. Ниже приведен пример того, что можно увидеть. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Войдите в учетную запись Azure с помощью **Connect-азаккаунт**.
5. На появившейся веб-странице вам будет предложено ввести учетные данные вашей учетной записи.

    * Кроме того, можно включить учетные данные учетной записи в качестве параметра в командлет **Connect-азаккаунт** с параметром **-Credential**.
    * Если вы являетесь партнером CSP, работающим от имени клиента, укажите клиента в качестве клиента, используя его идентификатор tenantID или основное доменное имя клиента. Например: **Connect-AzAccount-Tenant** fabrikam.com.

6. Свяжите подписку, которую вы хотите использовать с учетной записью, так как у учетной записи может быть несколько подписок.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик Служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Убедитесь, что поставщики успешно зарегистрированы:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Убедитесь, что в выходных данных команды **RegistrationState** изменения **зарегистрированы**. Если это не так, выполните командлет **Register-азресаурцепровидер** еще раз.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

1. Хранилище помещается в группу ресурсов. Если у вас нет группы ресурсов, создайте ее с помощью [New-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). В этом примере мы создадим новую группу ресурсов в регионе "Западная часть США".

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Используйте командлет [New-азрековерисервицесваулт](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) , чтобы создать хранилище. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Укажите тип избыточности, используемый для хранения хранилища.

   * [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md).
   * В следующем примере задается параметр **-BackupStorageRedundancy** для командлета[Set-азрековерисервицесбаккуппропертиес](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd для **testvault задано** , установленного в значение " **геоизбыточность**".

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выходные данные похожи на приведенные ниже. Обратите внимание, что предоставляются связанная группа ресурсов и расположение.

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

Сохраните объект хранилища в переменной и задайте контекст хранилища.

* Многие командлеты Azure Backup нуждаются в объекте хранилища служб восстановления в качестве входных данных, поэтому удобно хранить объект хранилища в переменной.
* Контекст хранилища — это тип данных, защищаемых в хранилище. Задайте его с помощью [Set-азрековерисервицесваултконтекст](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). После установки контекста он применяется ко всем последующим командлетам.

В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Получение идентификатора хранилища

Мы планируем использовать параметр контекста хранилища в соответствии с рекомендациями Azure PowerShell. Вместо этого можно сохранить или извлечь идентификатор хранилища и передать его в соответствующие команды. Поэтому, если вы не указали контекст хранилища или хотите указать команду для выполнения в определенном хранилище, передайте идентификатор хранилища как "-Ваултид" во всю соответствующую команду следующим образом:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

Политика архивации определяет расписание резервного копирования и время хранения резервных точек восстановления.

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* Просмотрите политику резервного копирования по умолчанию с помощью команды [Get-азрековерисервицесбаккупретентионполициобжект](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Просмотрите расписание политики архивации по умолчанию с помощью команды [Get-азрековерисервицесбаккупсчедулеполициобжект](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Для создания новой политики резервного копирования используется командлет [New-азрековерисервицесбаккуппротектионполици](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) . Введите расписание и объекты политики хранения.

По умолчанию время начала определяется в объекте политики Schedule. Используйте следующий пример, чтобы изменить время начала на нужное время начала. Требуемое время начала должно быть также в формате UTC. В приведенном ниже примере предполагается, что нужное время начала для ежедневного резервного копирования составляет 01:00 часов по ГРИНВИЧу.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Необходимо указать время начала в 30 минут, кратное. В приведенном выше примере это может быть только "01:00:00" или "02:30:00". Время начала не может быть "01:15:00"

В следующем примере показано сохранение политик расписания и хранения в переменных. Затем эти переменные используются в качестве параметров для новой политики (**невафсполици**). **NewAFSPolicy** выполняет ежедневное резервное копирование и хранит его в течение 30 дней.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выходные данные похожи на приведенные ниже.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Включение резервного копирования

После определения политики резервного копирования можно включить защиту для файлового ресурса Azure с помощью политики.

### <a name="retrieve-a-backup-policy"></a>Получение политики архивации

Получить соответствующий объект политики можно с помощью [Get-азрековерисервицесбаккуппротектионполици](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Используйте этот командлет для получения определенной политики или для просмотра политик, связанных с типом рабочей нагрузки.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Получение политики для типа рабочей нагрузки

В следующем примере извлекаются политики для типа рабочей нагрузки **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Выходные данные похожи на приведенные ниже.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Часовой пояс поля **BackupTime** в PowerShell — время в формате UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.

### <a name="retrieve-a-specific-policy"></a>Получение определенной политики

Следующая политика извлекает политику резервного копирования с именем **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Включение резервного копирования и применение политики

Включите защиту с помощью [Enable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). После того как политика будет связана с хранилищем, резервные копии будут активированы в соответствии с расписанием политики.

В следующем примере включается защита файлового ресурса Azure **тестазурефилешаре** в учетной записи хранения **тестсторажеаккт**с политикой **даиляфс**.

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

Используйте [BACKUP-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) для запуска резервного копирования по запросу для защищенного файлового ресурса Azure.

1. Получите учетную запись хранения и общую папку из контейнера в хранилище, в котором хранятся резервные копии данных, с помощью [Get-азрековерисервицесбаккупконтаинер](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Чтобы запустить задание резервного копирования, получите сведения о виртуальной машине с помощью командлета [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Выполните резервное копирование по запросу с помощью[BACKUP-азрековерисервицесбаккупитем](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Выполните резервное копирование по запросу следующим образом:

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

### <a name="using-on-demand-backups-to-extend-retention"></a>Использование резервных копий по запросу для расширения срока хранения

Резервные копии по запросу можно использовать для хранения моментальных снимков в течение 10 лет. Планировщики можно использовать для выполнения сценариев PowerShell по запросу с выбранным хранением и, таким же, создавать моментальные снимки через регулярные интервалы каждую неделю, месяц или год. При создании обычных моментальных снимков ознакомьтесь с [ограничениями резервного копирования по требованию](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) с помощью службы архивации Azure.

Если вы ищете примеры сценариев, см. пример скрипта на сайте GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup)> с помощью модуля Runbook службы автоматизации Azure, который позволяет планировать резервное копирование на периодической основе и хранить их даже до 10 лет.

### <a name="modify-the-protection-policy"></a>Изменение политики защиты

Чтобы изменить политику, используемую для резервного копирования файлового ресурса Azure, используйте [Enable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Укажите соответствующий элемент резервного копирования и новую политику резервного копирования.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Восстановление файловых ресурсов и файлов Azure

Можно восстановить весь файловый ресурс или отдельные файлы в общей папке. Можно выполнить восстановление в исходное расположение или в другое расположение.

### <a name="fetch-recovery-points"></a>Получение точек восстановления

Используйте [Get-азрековерисервицесбаккупрековерипоинт](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) , чтобы получить список всех точек восстановления для архивированного элемента.

В следующем скрипте:

* Переменная **$RP** представляет собой массив точек восстановления для выбранного элемента резервного копирования за последние семь дней.
* Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**.
* Используйте стандартное индексирование массива PowerShell для выбора точки восстановления.
* В примере **$rp[0]** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Выходные данные похожи на приведенные ниже.

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

После выбора соответствующей точки восстановления вы восстанавливаете общую папку или файл в исходное расположение или в альтернативное расположение.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Восстановление файлового ресурса Azure в альтернативное расположение

Используйте [RESTORE-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления в выбранную точку восстановления. Укажите эти параметры, чтобы определить альтернативное расположение:

* **Таржетсторажеаккаунтнаме**: учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **Таржетфилешаренаме**. файловые ресурсы в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**: папка в общей папке, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **ResolveConflict**: инструкция при конфликте с восстановленными данными. Принимает **перезаписать** или **пропустить**.

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

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Восстановление файла Azure в альтернативное расположение

Используйте [RESTORE-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления в выбранную точку восстановления. Укажите эти параметры, чтобы определить альтернативное расположение, и, чтобы однозначно определить файл, который требуется восстановить.

* **Таржетсторажеаккаунтнаме**: учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **Таржетфилешаренаме**. файловые ресурсы в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**: папка в общей папке, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**: абсолютный путь к файлу, восстанавливаемому в общей папке, в виде строки. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **Саурцефилетипе**: указывает, выбран ли каталог или файл. Принимает **папка** или **файл**.
* **ResolveConflict**: инструкция при конфликте с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Дополнительные параметры (SourceFilePath и Саурцефилетипе) относятся только к отдельному файлу, который требуется восстановить.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Эта команда возвращает задание с ИДЕНТИФИКАТОРом для трассировки, как показано в предыдущем разделе.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Восстановить файловые ресурсы и файлы Azure в исходном расположении

При восстановлении в исходное расположение не нужно указывать параметры, связанные с назначением и целевым объектом. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

Операции резервного копирования и восстановления по запросу возвращают задание вместе с ИДЕНТИФИКАТОРом, как показано при [выполнении резервного копирования по запросу](#trigger-an-on-demand-backup). Используйте командлет [Get-азрековерисервицесбаккупжобдетаилс](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) для отслеживания хода выполнения задания и сведений о нем.

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

## <a name="next-steps"></a>Дополнительная информация

[Сведения о](backup-azure-files.md) резервном копировании файлов Azure в портал Azure.
