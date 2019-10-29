---
title: Резервное копирование базы данных SQL в виртуальной машине Azure & восстановление с помощью PowerShell — Azure Backup
description: Резервное копирование и восстановление баз данных SQL на виртуальных машинах Azure с помощью Azure Backup и PowerShell.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure Backup; SQL
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: dacurwin
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 229d960f7851b5fab8504b6c2a109bece6c7b31f
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969091"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Резервное копирование и восстановление баз данных SQL на виртуальных машинах Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования и восстановления базы данных SQL в виртуальной машине Azure с помощью [Azure Backup](backup-overview.md) хранилища служб восстановления.

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
> * Настройте PowerShell и зарегистрируйте поставщик служб восстановления Azure.
> * Создайте хранилище служб восстановления.
> * Настройте резервное копирование для базы данных SQL на виртуальной машине Azure.
> * Запустите задание резервного копирования.
> * Восстановите резервную копию базы данных SQL.
> * Мониторинг заданий резервного копирования и восстановления.

## <a name="before-you-start"></a>Перед началом работы

* Дополнительные [сведения](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Узнайте о возможностях [резервного копирования SQL баз данных на виртуальных машинах Azure](backup-azure-sql-database.md#before-you-start).
* Проверьте иерархию объектов PowerShell для служб восстановления.

### <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Иерархия объектов представлена на следующей схеме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Ознакомьтесь со справочником по [командлету](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** в библиотеке Azure.

### <a name="set-up-and-install"></a>Установка и установка

Настройте PowerShell следующим образом.

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.5.0.

2. Найдите командлеты PowerShell Azure Backup с помощью следующей команды:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Проверьте псевдонимы и командлеты для Azure Backup и хранилища служб восстановления. Ниже приведен пример того, что можно увидеть. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Войдите в учетную запись Azure с помощью **Connect-азаккаунт**.
5. На появившейся веб-странице вам будет предложено ввести учетные данные вашей учетной записи.

    * Кроме того, можно включить учетные данные учетной записи в качестве параметра в командлет **Connect-азаккаунт** с параметром **-Credential**.
    * Если вы являетесь партнером CSP, работающим для клиента, укажите клиента в качестве клиента, используя его идентификатор клиента или основное доменное имя. Например: **Connect-AzAccount-Tenant** fabrikam.com.

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

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

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
    * В следующем примере задается параметр **-BackupStorageRedundancy** для командлета[Set-азрековерисервицесбаккуппроперти](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd для **testvault задано** , установленного в значение " **геоизбыточность**".

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выходные данные похожи на приведенные ниже. Предоставляются связанная группа ресурсов и расположение.

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

Мы планируем использовать параметр контекста хранилища в соответствии с рекомендациями Azure PowerShell. Вместо этого можно сохранить или извлечь идентификатор хранилища и передать его соответствующим командам следующим образом:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

Политика архивации определяет расписание резервного копирования и время хранения резервных точек восстановления.

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* Просмотрите политику резервного копирования по умолчанию с помощью команды [Get-азрековерисервицесбаккупретентионполициобжект](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Просмотрите расписание политики архивации по умолчанию с помощью команды [Get-азрековерисервицесбаккупсчедулеполициобжект](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Для создания новой политики резервного копирования используется командлет [New-азрековерисервицесбаккуппротектионполици](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) . Введите расписание и объекты политики хранения.

По умолчанию время начала определяется в объекте политики Schedule. Используйте следующий пример, чтобы изменить время начала на нужное время начала. Требуемое время начала должно быть также в формате UTC. В приведенном ниже примере предполагается, что нужное время начала для ежедневного резервного копирования составляет 01:00 часов по ГРИНВИЧу.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Необходимо указать время начала в 30 минут, кратное. В приведенном выше примере это может быть только "01:00:00" или "02:30:00". Время начала не может быть "01:15:00"

В следующем примере показано сохранение политик расписания и хранения в переменных. Затем эти переменные используются в качестве параметров для новой политики (**невсклполици**). **Невсклполици** занимает ежедневное полное резервное копирование, оставляет его в течение 180 дней и создает резервную копию журнала каждые 2 часа.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выходные данные похожи на приведенные ниже.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Включение резервного копирования

### <a name="registering-the-sql-vm"></a>Регистрация виртуальной машины SQL

Для резервного копирования виртуальных машин Azure и файловых ресурсов Azure служба резервного копирования может подключиться к этим Azure Resource Managerным ресурсам и получить соответствующие сведения. Поскольку SQL является приложением на виртуальной машине Azure, службе архивации требуется разрешение на доступ к приложению и получение необходимых сведений. Для этого необходимо *зарегистрировать* виртуальную машину Azure, СОДЕРЖАЩУЮ приложение SQL, с хранилищем служб восстановления. После регистрации виртуальной машины SQL с хранилищем вы можете защитить баз данных SQL только в этом хранилище. Для регистрации виртуальной машины используйте командлет [Register-азрековерисервицесбаккупконтаинер](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Команда вернет "контейнер резервной копии" этого ресурса, а состояние будет "зарегистрировано"

> [!NOTE]
> Если параметр Force не задан, пользователю предлагается подтвердить наличие текста "отключить защиту для этого контейнера". Проигнорируйте этот текст и скажите "Y" для подтверждения. Это известная проблема, и мы работаем над удалением текста и требованием параметра Force.

### <a name="fetching-sql-dbs"></a>Получение баз данных SQL

После завершения регистрации служба архивации сможет перечислить все доступные компоненты SQL в виртуальной машине. Чтобы просмотреть все компоненты SQL, для которых еще не требуется резервное копирование в это хранилище, используйте командлет [Get-азрековерисервицесбаккуппротектаблеитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS.

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

В выходных данных будут показаны все незащищенные компоненты SQL для всех виртуальных машин SQL, зарегистрированных в этом хранилище, с типом элемента и ServerName. Можно дополнительно отфильтровать определенную виртуальную машину SQL, передав параметр "-Container" или указав сочетание "Name" и "ServerName" вместе с флагом ItemType, чтобы приступить к уникальному элементу SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Настройка резервного копирования

Теперь, когда у нас есть необходимая база данных SQL и политика, с которой необходимо создать резервную копию, можно использовать командлет [Enable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) , чтобы настроить резервное копирование для этой базы данных SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Команда ожидает, пока не завершится Настройка резервного копирования и вернет следующие выходные данные.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Получение новых баз данных SQL

После регистрации компьютера служба Backup Service выберет сведения о доступных баз данных. Если пользователь добавляет экземпляры SQL баз данных/SQL в зарегистрированный компьютер позже, необходимо вручную запустить службу резервного копирования для выполнения нового запроса, чтобы получить все незащищенные баз данных (включая вновь добавленные). Используйте командлет [Initialize-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS на ВИРТУАЛЬНОЙ машине SQL для выполнения нового запроса. Команда ожидает завершения операции. Позже используйте командлет [Get-азрековерисервицесбаккуппротектаблеитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS, чтобы получить список последних незащищенных компонентов SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

После получения соответствующих защищаемых элементов включите резервное копирование, как [описано в приведенном выше разделе](#configuring-backup).
Если один из них не хочет вручную определять новые баз данных, он может выбрать автоматическую защиту, как описано [ниже](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Включить автозащиту

Пользователь может настроить резервное копирование так, что все баз данных, добавленные в будущем, автоматически защищаются с помощью определенной политики. Чтобы включить автозащиту, используйте командлет [Enable-азрековерисервицесбаккупаутопротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Так как инструкция предназначена для резервного копирования всех будущих баз данных, операция выполняется на уровне SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

После того как цель автоматической защиты задана, запрос к компьютеру для получения только что добавленного баз данных выполняется как запланированная фоновая задача каждые 8 часов.

## <a name="restore-sql-dbs"></a>Восстановление SQL баз данных

Azure Backup можете восстановить базы данных SQL Server, работающие на виртуальных машинах Azure, следующим образом:

1. Восстановление до определенной даты или времени (во второй) с помощью резервных копий журнала транзакций. Azure Backup автоматически определяет соответствующую полную разностную резервную копию и цепочку резервных копий журналов, которые требуются для восстановления в зависимости от выбранного времени.
2. Восстановление определенной полной или разностной резервной копии для восстановления до определенной точки восстановления.

Перед восстановлением SQL баз данных проверьте предварительные требования, указанные [здесь](restore-sql-database-azure-vm.md#prerequisites) .

Сначала извлеките соответствующую резервную копию базы данных SQL с помощью командлета [Get-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Получение соответствующего времени восстановления

Как описано выше, пользователь может восстановить резервную копию базы данных SQL в полную или разностную копию **или** на момент времени в журнале.

#### <a name="fetch-distinct-recovery-points"></a>Получение уникальных точек восстановления

Используйте [Get-азрековерисервицесбаккупрековерипоинт](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) для получения уникальных (полных или разностных) точек восстановления для резервной копии базы данных SQL.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Выходные данные похожи на приведенный ниже пример.

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Используйте фильтр "RecoveryPointId" или фильтр массива для выборки соответствующей точки восстановления.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Получение точки восстановления до точки во времени

Если пользователь хочет восстановить базу данных на определенный момент времени, используйте командлет [Get-азрековерисервицесбаккупрековерилогчаин](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS. Командлет возвращает список дат, представляющих время начала и окончания неразрывной непрерывной цепочки журналов для этого элемента резервного копирования SQL. Требуемый момент времени должен находиться в пределах этого диапазона.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Выходные данные будут выглядеть так, как показано в следующем примере.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Приведенные выше выходные данные означают, что пользователь может выполнить восстановление на любой момент времени между отображаемым временем начала и временем окончания. Время задаются в формате UTC. Создайте любую точку во времени в PS, которая находится в диапазоне, показанном выше.

> [!NOTE]
> Если для восстановления выбран параметр точка входа в систему, пользователю не нужно указывать начальную точку, т. е. полную резервную копию, из которой восстанавливается база данных. Azure Backup служба позаботится о полном плане восстановления, т. е. какой полной резервной копии следует выбрать, какие резервные копии журналов будут применяться и т. д.

### <a name="determine-recovery-configuration"></a>Определение конфигурации восстановления

В случае восстановления базы данных SQL поддерживаются следующие сценарии восстановления.

1. Переопределение резервной копии базы данных SQL данными из другой точки восстановления — Оригиналворклоадресторе
2. Восстановление базы данных SQL в качестве новой базы данных в том же экземпляре SQL — Алтернатеворклоадресторе
3. Восстановление базы данных SQL в качестве новой базы данных в другом экземпляре SQL в другой виртуальной машине SQL — Алтернатеворклоадресторе

После получения соответствующей точки восстановления (DISTINCT или Log-On-Time) используйте командлет [Get-азрековерисервицесбаккупворклоадрековериконфиг](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS, чтобы получить объект конфигурации восстановления в соответствии с требуемым планом восстановления.

#### <a name="original-workload-restore"></a>Восстановление исходной рабочей нагрузки

Чтобы переопределить резервную базу данных с данными из точки восстановления, просто укажите правильный флаг и соответствующую точку восстановления, как показано в следующих примерах.

##### <a name="original-restore-with-distinct-recovery-point"></a>Исходное восстановление с отдельной точкой восстановления

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Исходное восстановление с точкой входа в систему

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Восстановление альтернативной рабочей нагрузки

> [!IMPORTANT]
> Резервную копию базы данных SQL можно восстановить в качестве новой базы данных только для другого SQLInstance, на виртуальной машине Azure, зарегистрированной в этом хранилище.

Как описано выше, если Целевая SQLInstance находится на другой виртуальной машине Azure, убедитесь, что она [зарегистрирована в этом хранилище](#registering-the-sql-vm) , а соответствующая SQLInstance отображается как защищаемый элемент.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Затем просто передайте соответствующую точку восстановления, целевой экземпляр SQL с правым флагом, как показано ниже.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Альтернативное восстановление с отдельной точкой восстановления

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Альтернативное восстановление с указанием времени входа в систему

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Конечный объект конфигурации точки восстановления, полученный из командлета [Get-азрековерисервицесбаккупворклоадрековериконфиг](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS, имеет всю необходимую информацию для восстановления и, как показано ниже.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Вы можете изменить поля имя восстановленной базы данных, Овервритевлифпресент, Норековеримоде и Таржетфисикалпас. Получите дополнительные сведения о путях к целевым файлам, как показано ниже.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Задайте соответствующие свойства PS как строковые значения, как показано ниже.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Убедитесь, что окончательный объект конфигурации восстановления имеет все необходимые и правильные значения, так как операция восстановления будет основана на объекте конфигурации.

### <a name="restore-with-relevant-configuration"></a>Восстановление с соответствующей конфигурацией

После получения и проверки соответствующего объекта конфигурации восстановления используйте командлет [RESTORE-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS, чтобы начать процесс восстановления.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Операция восстановления возвращает задание, которое необходимо отвести.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Управление резервными копиями SQL

### <a name="on-demand-backup"></a>Резервное копирование по запросу

После включения резервного копирования для базы данных пользователь может также активировать резервное копирование базы данных по запросу с помощью командлета [BACKUP-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. В следующем примере запускается полная резервная копия в базе данных SQL с включенным сжатием, а полная резервная копия должна храниться в течение 60 дней.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Команда нерегламентированного резервного копирования возвращает задание, которое необходимо отвести.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Если выходные данные потеряны или вы хотите получить соответствующий идентификатор задания, [получите список заданий](#track-azure-backup-jobs) от Azure Backup службы, а затем отследите их и сведения о них.

### <a name="change-policy-for-backup-items"></a>Изменение политики для элементов архивации

Пользователь может изменить существующую политику или изменить политику резервного элемента с Policy1 на Policy2. Чтобы переключить политики для архивированного элемента, извлеките соответствующую политику и элемент резервного копирования и используйте команду [Enable-азрековерисервицес](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) с элементом Backup в качестве параметра.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Команда ожидает, пока не завершится Настройка резервного копирования и вернет следующие выходные данные.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Повторная регистрация виртуальных машин SQL

> [!WARNING]
> Не забудьте прочитать этот [документ](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , чтобы понять признаки сбоя и причины, прежде чем пытаться повторно зарегистрировать

Чтобы активировать повторную регистрацию виртуальной машины SQL, извлеките соответствующий контейнер резервного копирования и передайте его в командлет Register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>остановка защиты;

#### <a name="retain-data"></a>Хранить данные

Если пользователь желает отключить защиту, он может использовать командлет [Disable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Это приведет к отмене запланированных резервных копий, но данные будут сохранены до тех пор, пока не будет храниться неограниченное время.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>удаление резервных копий;

Чтобы полностью удалить сохраненные резервные копии данных в хранилище, просто добавьте флаг "-Ремоверековерипоинтс" или переключитесь в [команду "Disable"](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Отключить автоматическую защиту

Если для SQLInstance была настроена автозащита, пользователь может отключить ее с помощью командлета [Disable-азрековерисервицесбаккупаутопротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Отмена регистрации виртуальной машины SQL

Если все баз данных SQL Server [больше не защищены и данные резервного копирования не существуют](#delete-backup-data), пользователь может отменить регистрацию ВИРТУАЛЬНОЙ машины SQL из этого хранилища. Только пользователь может защищать баз данных в другом хранилище. Используйте командлет [Unregister-азрековерисервицесбаккупконтаинер](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS, чтобы отменить регистрацию ВИРТУАЛЬНОЙ машины SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Мониторинг Azure Backup заданий

Важно отметить, что Azure Backup отслеживает только активируемые пользователем задания в резервной копии SQL. Запланированные резервные копии (включая резервные копии журналов) не отображаются на портале или в PowerShell. Однако при сбое запланированных заданий создается [оповещение о резервном копировании](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) , которое отображается на портале. [Используйте Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) для трассировки всех запланированных заданий и других важных сведений.

Пользователи могут отслеживанию нерегламентированных или пользовательских операций, активируемых пользователем, с JobID, возвращаемым в [выходных данных](#on-demand-backup) асинхронных заданий, таких как Backup. Используйте командлет [Get-азрековерисервицесбаккупжобдетаил](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS для трассировки задания и его сведений.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Чтобы получить список нерегламентированных заданий и их состояний из Azure Backup службы, используйте командлет [Get-азрековерисервицесбаккупжоб](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. В следующем примере возвращаются все выполняющиеся задания SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Чтобы отменить выполняющееся задание, используйте командлет " [остановить-азрековерисервицесбаккупжоб](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS".

## <a name="managing-sql-always-on-availability-groups"></a>Управление группами доступности SQL Always On

Для групп доступности SQL Always On убедитесь, что [зарегистрированы все узлы](#registering-the-sql-vm) группы доступности (AG). После завершения регистрации для всех узлов объект группы доступности SQL логически создается в разделе защищаемые элементы. Базы данных в группе доступности SQL будут перечислены как "SQLDatabase". Узлы будут отображаться как автономные экземпляры, а базы данных SQL по умолчанию в них будут перечислены также как базы данных SQL.

Например, предположим, что в группе доступности SQL есть два узла: "SQL-Server-0" и "SQL-Server-1" и 1 база данных SQL AG. Как только эти узлы будут зарегистрированы, если пользователь [выведет список защищаемых элементов](#fetching-sql-dbs), в нем будут перечислены следующие компоненты.

1. Объект SQL AG — защищаемый тип элемента как SQLAvailabilityGroup
2. Тип защищаемого элемента базы данных SQL AG в виде SQLDatabase
3. SQL-Server-0 — Тип защищаемого элемента — SQLInstance
4. SQL-Server-1 — Тип защищаемого элемента — SQLInstance
5. Все значения по умолчанию SQL баз данных (Master, Model, msdb) в SQL-Server-0 — защищаемый тип элемента как SQLDatabase
6. Все значения по умолчанию SQL баз данных (Master, Model, msdb) в SQL-Server-1 — защищаемый тип элемента как SQLDatabase

SQL-Server-0, SQL-Server-1 также будет отображаться как "Азуревмаппконтаинер" при [отображении контейнеров резервного копирования](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Просто извлеките соответствующую базу данных SQL, чтобы [включить резервное копирование](#configuring-backup) , и командлеты [нерегламентированного резервного копирования](#on-demand-backup) и [восстановления PowerShell](#restore-sql-dbs) идентичны.
