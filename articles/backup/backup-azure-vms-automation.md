---
title: Развертывание архивации виртуальных машин, развернутых посредством Resource Manager, и управление ею с помощью PowerShell
description: Использование PowerShell для развертывания архивации виртуальных машин, развернутых посредством Resource Manager, и управления ею
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa53a7e598b42e93e86eb059c36ff89f38bb7093
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300597"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Использование PowerShell для резервного копирования и восстановления виртуальных машин

В этой статье показано, как выполнять архивацию и восстановление виртуальной машины Azure из хранилища служб восстановления с помощью командлетов Azure PowerShell. Хранилище служб восстановления — это ресурс Azure Resource Manager, используемый для защиты данных и ресурсов-контейнеров в службе архивации Azure и службах Azure Site Recovery.

> [!NOTE]
> В Azure предусмотрены две модели развертывания, позволяющие создавать ресурсы и работать с ними: [модель развертывания с помощью Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование виртуальных машин, созданных с помощью модели Resource Manager.
>
>

В этой статье описано, как использовать PowerShell для защиты виртуальной машины и восстановления данных из точки восстановления.

## <a name="concepts"></a>Основные понятия

Если вы не знакомы со службой архивации Azure, общие сведения о службе см. в статье [Что такое служба архивации Azure?](backup-introduction-to-azure-backup.md) Перед началом работы убедитесь, что вам известны предварительные требования для работы со службой архивации Azure и ограничения, применяемые к текущему решению для архивации виртуальных машин.

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, с чего следует начать.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Справку по командлету PowerShell AzureRm.RecoveryServices.Backup см. в статье [Azure Backup — Recovery Services Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) (Командлеты служб архивации и восстановления Azure) в библиотеке Azure.

## <a name="setup-and-registration"></a>Настройка и регистрация

Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (требуется версия 1.4.0 или более поздняя)

2. Чтобы получить список доступных командлетов PowerShell для службы архивации Azure, введите следующую команду:

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Отображаются псевдонимы и командлеты для службы архивации Azure, Azure Site Recovery и хранилища служб восстановления. Ниже приведен пример того, что вы увидите. Это не полный список командлетов.

    ![Список служб восстановления](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Чтобы войти в учетную запись Azure, используйте командлет **Connect-AzureRmAccount**. Откроется веб-станица, на которой пользователю предлагается ввести данные для входа в учетную запись:

    * Кроме того, учетные данные можно добавить в качестве параметра в командлет **Connect-AzureRmAccount**, используя параметр **-Credential**.
    * Если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента. Для этого нужно ввести идентификатор или основное доменное имя клиента. Например:  **Connect-AzureRmAccount -Tenant "fabrikam.com"**.

4. Свяжите подписку, которую собираетесь использовать, с учетной записью, так как последняя может иметь несколько подписок:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Если вы используете службу архивации Azure впервые, выполните командлет **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**, чтобы зарегистрировать поставщик служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Вы можете проверить, зарегистрированы ли поставщики, выполнив следующие команды:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    В выходных данных команды для **RegistrationState** должно быть установлено значение **Registered**. Если это не так, повторно выполните командлет **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**.

С помощью PowerShell можно автоматизировать следующие задачи:

* [создание хранилища служб восстановления](backup-azure-vms-automation.md#create-a-recovery-services-vault);
* [Резервное копирование виртуальных машин Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [активация задания резервного копирования](backup-azure-vms-automation.md#trigger-a-backup-job);
* [наблюдение за выполнением задания резервного копирования](backup-azure-vms-automation.md#monitoring-a-backup-job);
* [Восстановление виртуальной машины Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Чтобы создать хранилище служб восстановления, выполните описанные ниже действия. Хранилище служб восстановления отличается от хранилища службы архивации.

1. Хранилище служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. При создании группы ресурсов укажите ее имя и расположение.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Воспользуйтесь командлетом **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)**, чтобы создать хранилище служб восстановления. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Укажите необходимый тип избыточности хранилища: [локально избыточное (LRS)](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное (GRS)](../storage/common/storage-redundancy-grs.md). В следующем примере показано, что для параметра BackupStorageRedundancy для testvault задано значение GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных. По этой причине объект хранилища служб восстановления резервных копий удобно хранить в переменной.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**, вы получите список всех хранилищ в подписке.

```powershell
Get-AzureRmRecoveryServicesVault
```

Результат будет аналогичен приведенному ниже; обратите внимание, что указаны соответствующие ResourceGroupName и Location.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Резервное копирование виртуальных машин Azure

Используйте хранилище служб восстановления для защиты виртуальной машины. Прежде чем применить защиту, задайте контекст хранилища (тип данных, защиту которых обеспечивает хранилище) и проверьте политику защиты. Политика защиты — это график выполнения заданий резервного копирования и срок хранения каждого моментального снимка резервной копии.

### <a name="set-vault-context"></a>Задание контекста хранилища

Перед включением защиты на виртуальной машине выполните командлет **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**, чтобы задать контекст хранилища. Заданный контекст хранилища применяется ко всем последующим командлетам. В следующем примере задается контекст для хранилища *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Создание политики защиты

Создаваемое хранилище служб восстановления поставляется с политиками защиты и хранения по умолчанию. Политика защиты по умолчанию запускает задание резервного копирования каждый день в указанное время. Политика хранения по умолчанию хранит ежедневную точку восстановления в течение 30 дней. С помощью политики по умолчанию можно быстро защитить виртуальную машину. Кроме того, политику можно изменить позже, задав другие сведения.

Для получения списка политик хранения в хранилище используется командлет **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**. С его помощью можно получить конкретную политику или просмотреть политики, связанные с типом рабочей нагрузки. В следующем примере возвращаются политики для типа рабочей нагрузки AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Часовой пояс поля BackupTime в PowerShell — UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.
>
>

Политика защиты архивации связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления. Для просмотра политики хранения по умолчанию используется командлет **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)**.  Аналогичным образом, для получения политики расписания по умолчанию используется командлет **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)**. Командлет **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** создает объект PowerShell, который содержит сведения о политике архивации. Объекты политик расписания и хранения используются в качестве входных данных в командлете **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. В следующем примере показано сохранение политик расписания и хранения в переменных. В примере эти переменные используются для определения параметров при создании политики защиты *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Включить защиту

После определения политики защиты по-прежнему необходимо включить политику для элемента. Для включения защиты используйте командлет **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**. Защита включается для двух объектов: элемента и политики. После того как политика сопоставится с хранилищем, рабочий процесс архивации будет активироваться по времени, определенному в политике расписания.

В следующих примерах включается защита для элемента V2VM с помощью политики NewPolicy. Примеры отличаются в зависимости от того, зашифрована ли виртуальная машина и какой у нее тип шифрования.

Включение защиты на **виртуальных машинах Resource Manager без шифрования**

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Чтобы включить защиту на **зашифрованных виртуальных машинах (зашифрованных с помощью BEK и KEK)**, необходимо предоставить службе архивации Azure разрешения на доступ для чтения ключей и секретов из хранилища ключей.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Чтобы включить защиту на **зашифрованных виртуальных машинах (зашифрованных только с помощью BEK)**, необходимо предоставить службе архивации Azure разрешения на доступ для чтения секретов из хранилища ключей.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Если вы работаете в облаке Azure для государственных организаций, используйте значение ff281ffe-705c-4f53-9f37-a40e6f2c68f3 для параметра **-ServicePrincipalName** в командлете [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Чтобы включить защиту на классической виртуальной машине:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Изменение политики защиты

Чтобы изменить политику защиты, используйте командлет [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) для изменения объектов SchedulePolicy или RetentionPolicy.

В следующем примере количество дней хранения точки восстановления изменяется на 365.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Активация архивации

Для активации задания архивации используйте командлет **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**. Если это начальная архивация, она будет полной. При последующем выполнении архивации резервная копия будет добавочной. Перед запуском задания резервного копирования выполните командлет **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**, чтобы задать контекст хранилища. В следующем примере предполагается, что контекст хранилища уже был задан.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Часовой пояс для полей StartTime и EndTime в PowerShell — UTC. Однако при отображении времени на портале Azure время меняется в соответствии с локальным часовым поясом.
>
>

## <a name="monitoring-a-backup-job"></a>Наблюдение за выполнением задания резервного копирования

Вы можете отслеживать длительные операции, например задания резервного копирования, без использования портала Azure. Чтобы получить последнее состояние выполняющегося задания, используйте командлет **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Этот командлет возвращает задания резервного копирования для конкретного хранилища, и это хранилище указывается в контексте хранилища. Следующий пример возвращает состояние выполняющегося задания в виде массива и сохраняет состояние в переменной $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Вместо опроса этих заданий о ходе их выполнения, который требует выполнения дополнительного кода, используйте командлет **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Восстановление виртуальной машины Azure

Операции восстановления виртуальной машины через портал Azure и с помощью Azure PowerShell существенно отличаются. При использовании PowerShell операция восстановления завершается созданием дисков и сведений о конфигурации из точки восстановления. Виртуальная машина при операции восстановления не создается. Чтобы создать виртуальную машину с диска, см. раздел [Создание виртуальной машины с восстановленного диска](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Чтобы восстановить несколько файлов из резервной копии виртуальной машины Azure без восстановления всей виртуальной машины, изучите раздел о [восстановлении файлов](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Виртуальная машина при этой операции не создается.
>
>

На представленной ниже схеме показана иерархия объектов от RecoveryServicesVault до BackupRecoveryPoint.

![Иерархия объектов служб восстановления с BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Чтобы восстановить данные резервной копии, определите архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Выполните командлет **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**, чтобы восстановить данные из хранилища в свою учетную запись.

Ниже перечислены основные шаги для восстановления виртуальной машины Azure.

* Выберите виртуальную машину.
* Выберите точку восстановления.
* Восстановите диски.
* Создайте виртуальную машину с хранимых дисков.

### <a name="select-the-vm"></a>Выбор виртуальной машины.

Чтобы получить объект PowerShell, определяющий правильный архивный элемент, начните с контейнера в хранилище и пройдите постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** и передайте найденный контейнер в командлет **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Выбор точки восстановления

Выполните командлет **[Get-AzureRMRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)**, чтобы получить полный список точек восстановления. Выберите нужную точку восстановления. Если вы не знаете, какую точку восстановления выбрать, используйте последнюю точку RecoveryPointType = AppConsistent в списке.

В следующем сценарии переменная **$rp**представляет собой массив точек восстановления для выбранного архивного элемента за последние семь дней. Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например, $rp[0] обозначает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Восстановление дисков

Выполните командлет **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**, чтобы восстановить данные и конфигурацию архивного элемента до точки восстановления. Используйте выбранную точку восстановления как значение для параметра **-RecoveryPoint**. В предыдущем примере была выбрана точка восстановления **$rp[0]**. В следующем примере кода **$rp[0]** является точкой восстановления, которая используется для восстановления диска.

Восстановление дисков и сведений о конфигурации

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Восстановление управляемых дисков

> [!NOTE]
> Если в резервной копии виртуальной машины содержатся управляемые диски и их необходимо восстановить в качестве управляемых дисков, вы можете это сделать с помощью модуля RM Azure PowerShell 6.7.0. и более поздних версий.
>
>

Укажите дополнительный параметр **TargetResourceGroupName** для указания группы ресурсов, в которой будут восстановлены управляемые диски. 

> [!NOTE]
> Настоятельно рекомендуется использовать параметр **TargetResourceGroupName** для восстановления управляемых дисков, поскольку это приведет к значительному повышению производительности. Для модуля Az Azure Powershell, начиная с версии 1.0, этот параметр является обязательным в случае восстановления с управляемых дисков.
>
>


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Файл **VMConfig.JSON** будет восстановлен в учетной записи хранения, а управляемые диски — в указанной целевой группе ресурсов.

Вы должны увидеть результат, аналогичный приведенному ниже.

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

С помощью командлета **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** можно дождаться завершения задания восстановления.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Когда задание восстановления будет выполнено, выполните командлет **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)**, чтобы получить сведения об операции восстановления. Свойство JobDetails содержит сведения, необходимые для повторного создания виртуальной машины.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Восстановив диски, перейдите к следующему разделу по созданию виртуальной машины.

## <a name="create-a-vm-from-restored-disks"></a>Создание виртуальной машины с восстановленного диска

После восстановления дисков создайте и настройте виртуальную машину с диска, выполнив описанные ниже действия.

> [!NOTE]
> При создании зашифрованных виртуальных машин с помощью восстановленных дисков у роли Azure должно быть разрешение на выполнение действия **Microsoft.KeyVault/vaults/deploy/action**. Если у роли нет этого разрешения, создайте пользовательскую роль с этим действием. Дополнительные сведения см. в разделе [Пользовательские роли в Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> После восстановления дисков можно получить шаблон развертывания, который можно использовать непосредственно для создания виртуальной машины. Для создания управляемых или неуправляемых и зашифрованных или незашифрованных виртуальных машин больше не используются разные командлеты PS.

В сведениях о результирующем задании указан URI шаблона, который можно запросить и развернуть.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Чтобы создать виртуальную машину, просто разверните шаблон, как описано [здесь](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

В следующем разделе перечислены шаги, необходимые для создания виртуальной машины с помощью файла VMConfig.

> [!NOTE]
> Для создания виртуальной машины настоятельно рекомендуется использовать шаблон развертывания, описанный выше. Действия, указанные в этом разделе (пункты 1–6), скоро перестанут использоваться.

1. Запросите свойства восстановленного диска для получения сведений о задании.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Задайте контекст хранилища Azure и восстановите файл конфигурации JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Используйте файл конфигурации JSON для создания конфигурации виртуальной машины.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Подключите диск операционной системы и диски данных. Здесь приводятся примеры для различных конфигураций управляемых и зашифрованных виртуальных машин. Используйте пример, подходящий для вашей конфигурации виртуальных машин.

   * Используйте следующий пример для **неуправляемой виртуальной машины без шифрования**.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Для неуправляемых зашифрованных виртуальных машин с Azure AD (зашифрованных только с помощью BEK)** необходимо восстановить секрет в хранилище ключей до подключения дисков. Дополнительные сведения см. в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). В следующем примере показано, как подключить диски операционной системы и диски данных к зашифрованной виртуальной машине. При указании диска операционной системы указывайте соответствующий тип операционной системы.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Для неуправляемых зашифрованных виртуальных машин с Azure AD (зашифрованных с помощью BEK и KEK)** необходимо восстановить ключ и секрет в хранилище ключей до подключения дисков. Дополнительные сведения см. в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). В следующем примере показано, как подключить диски операционной системы и диски данных к зашифрованной виртуальной машине.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Для неуправляемых зашифрованных виртуальных машин без Azure AD (зашифрованных только с помощью BEK)**, если в исходном **хранилище ключей недоступны секреты**, восстановите секреты в хранилище ключей, выполнив действия, описанные в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). Затем выполните следующие скрипты, чтобы задать сведения о шифровании для большого двоичного объекта с восстановленной операционной системой (этот шаг необязателен для больших двоичных объектов данных). $dekurl можно извлечь из восстановленного хранилища ключей.<br>

   Приведенный ниже скрипт нужно выполнять только в том случае, если секреты недоступны в исходном хранилище ключей.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    После того как **секреты станут доступны** и будут установлены сведения о шифровании для большого двоичного объекта операционной системы, подключите диски, используя скрипт, приведенный ниже.<br>

    Если в исходном хранилище ключей секреты уже доступны, то приведенный выше скрипт выполнять не нужно.

      ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Для неуправляемых зашифрованных виртуальных машин без Azure AD (зашифрованных с помощью BEK и KEK)**, если в исходном **хранилище ключей недоступны ключи и секреты**, восстановите ключи и секреты в хранилище ключей, выполнив действия, описанные в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). Затем выполните следующие скрипты, чтобы задать сведения о шифровании для большого двоичного объекта с восстановленной операционной системой (этот шаг необязателен для больших двоичных объектов данных). $dekurl и $kekurl можно извлечь из восстановленного хранилища ключей.

   Приведенный ниже скрипт нужно выполнять только в том случае, если в исходном хранилище ключей недоступны ключ и секрет.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   После того **как ключи или секреты станут доступны** и будут установлены сведения о шифровании для большого двоичного объекта операционной системы, подключите диски, используя скрипт, приведенный ниже.

    Если в исходном хранилище ключей уже доступны ключи и секреты, то приведенный выше скрипт выполнять не нужно.

    ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

  * **Управляемые и незашифрованные виртуальные машины**. Для управляемых незашифрованных виртуальных машин подключите восстановленные управляемые диски. Более подробные сведения см. в статье [Подключение диска данных к виртуальной машине Windows с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **Для управляемых зашифрованных виртуальных машин с Azure AD (зашифрованных только с помощью BEK)** подключите восстановленные управляемые диски. Более подробные сведения см. в статье [Подключение диска данных к виртуальной машине Windows с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **Для управляемых зашифрованных виртуальных машин с Azure AD (зашифрованных с помощью BEK и KEK)** подключите восстановленные управляемые диски. Более подробные сведения см. в статье [Подключение диска данных к виртуальной машине Windows с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **Для управляемых зашифрованных виртуальных машин без Azure AD (зашифрованных только с помощью BEK)**, если в исходном **хранилище ключей недоступны секреты**, восстановите секреты в хранилище ключей, выполнив действия, описанные в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). Затем выполните следующие скрипты, чтобы задать сведения о шифровании для восстановленного диска операционной системы (этот шаг не является обязательным для диска данных). $dekurl можно извлечь из восстановленного хранилища ключей.

    Приведенный ниже скрипт нужно выполнять только в том случае, если секреты недоступны в исходном хранилище ключей.  

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

    После того как секреты станут доступны и на диске ОС будут установлены сведения о шифровании, выполните действия для подключения восстановленных управляемых дисков, приведенные в статье [Подключение диска данных к виртуальной машине Windows с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **Для управляемых зашифрованных виртуальных машин без Azure AD (зашифрованных с помощью BEK и KEK)**, если в исходном **хранилище ключей недоступны ключи и секреты**, восстановите ключи и секреты в хранилище ключей, выполнив действия, описанные в статье [Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы Azure Backup](backup-azure-restore-key-secret.md). Затем выполните следующие скрипты, чтобы задать сведения о шифровании для восстановленного диска операционной системы (этот шаг не является обязательным для диска данных). $dekurl и $kekurl можно извлечь из восстановленного хранилища ключей.

  Приведенный ниже скрипт нужно выполнять только в том случае, если в исходном хранилище ключей недоступны ключ и секрет.

  ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzureRmDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    После того как секреты станут доступны и на диске ОС будут установлены сведения о шифровании, выполните действия для подключения восстановленных управляемых дисков, приведенные в статье [Подключение диска данных к виртуальной машине Windows c помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Задайте параметры сети.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Создайте виртуальную машину.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Отправьте расширение ADE.

  * **Для виртуальных машин с Azure AD** используйте следующую команду, чтобы вручную включить шифрование для дисков данных.  

    **Виртуальные машины, использующие только BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

    **Виртуальные машины, использующие BEK и KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

  * **Для виртуальных машин без Azure AD** используйте следующую команду, чтобы вручную включить шифрование для дисков данных.

    Если во время выполнения команды запрашивается AADClientID, необходимо обновить Azure PowerShell.

    **Виртуальные машины, использующие только BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **Виртуальные машины, использующие BEK и KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Восстановление файлов из резервной копии виртуальной машины Azure

Из резервной копии виртуальной машины Azure можно восстанавливать не только диски, но и отдельные файлы. Функция восстановления отдельных файлов предоставляет доступ ко всем файлам в точке восстановления. Управление файлами ведется через проводник, как обычно.

Основные шаги по восстановлению файла из резервной копии виртуальной машины Azure:

* Выбор виртуальной машины.
* Выбор точки восстановления
* Подключение дисков точки восстановления
* Копирование необходимых файлов.
* Отключение диска.

### <a name="select-the-vm"></a>Выбор виртуальной машины.

Чтобы получить объект PowerShell, определяющий правильный архивный элемент, начните с контейнера в хранилище и пройдите постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** и передайте найденный контейнер в командлет **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Выбор точки восстановления

Выполните командлет **[Get-AzureRMRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)**, чтобы получить полный список точек восстановления. Выберите нужную точку восстановления. Если вы не знаете, какую точку восстановления выбрать, используйте последнюю точку RecoveryPointType = AppConsistent в списке.

В следующем сценарии переменная **$rp**представляет собой массив точек восстановления для выбранного архивного элемента за последние семь дней. Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например, $rp[0] обозначает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Подключение дисков точки восстановления

Используйте командлет **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)**, чтобы получить скрипт для подключения всех дисков точки восстановления.

> [!NOTE]
> Диски подключаются к виртуальной машине, на которой выполняется скрипт, в качестве присоединенных дисков iSCSI. Подключение выполняется немедленно, плата не взимается.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Запустите скрипт на виртуальной машине, на которой нужно восстановить файлы. Чтобы выполнить сценарий, необходимо ввести предоставленный пароль. Присоединив диски, используйте проводник Windows для просмотра новых томов и файлов. Дополнительные сведения см. в статье [Восстановление файлов из резервной копии виртуальной машины Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Отключение дисков

Скопировав необходимые файлы, отключите диски с помощью командлета **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)**. Убедитесь, что диски отключены, чтобы удалить доступ к файлам точки восстановления.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Дополнительная информация

Если вы предпочитаете использовать PowerShell для взаимодействия с ресурсами Azure, см. статью [Развертывание резервного копирования в Azure для Windows Server или клиента Windows и управление им с помощью PowerShell](backup-client-automation.md). Сведения об управлении резервными копиями DPM см. в статье [Развертывание службы архивации для DPM и управление ею](backup-dpm-automation.md). Обе эти статьи имеют две версии — для развертывания с помощью Resource Manager и для классической модели развертывания.  
