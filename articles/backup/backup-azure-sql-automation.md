---
title: СЗЛ DB в резервном копировании Azure VM & восстановить через PowerShell
description: Резервное копирование и восстановление баз данных S'L в Azure VMs с помощью резервного копирования Azure и PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131814"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Резервное копирование и восстановление баз данных S'L в Azure VMs с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования и восстановления СЗЛ DM в Azure VM с помощью хранилища служб восстановления [резервного копирования Azure.](backup-overview.md)

В статье описывается выполнение следующих задач:

> [!div class="checklist"]
>
> * Навлажь PowerShell и зарегистрируйте поставщика услуг восстановления Azure.
> * Создайте хранилище служб восстановления,
> * Наконудируй резервную перепорку для S'L DB в Azure VM.
> * Выполнить работу резервного копирования.
> * Восстановление резервного копирования S'L DB.
> * Мониторинг резервного копирования и восстановления заданий.

## <a name="before-you-start"></a>Перед началом работы

* [Узнайте больше](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Ознакомьте возможности функций для [резервного копирования D-сообщений s'L в Azure VMs.](backup-azure-sql-database.md#before-you-start)
* Просмотрите иерархию объектов PowerShell для служб восстановления.

### <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Иерархия объектов обобщена в следующей диаграмме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Просмотрите ссылку **на ссылку Az.RecoveryServices** [cmdlet](/powershell/module/az.recoveryservices) в библиотеке Azure.

### <a name="set-up-and-install"></a>Настройка и установка

Настройка PowerShell следующим образом:

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия составляет 1.5.0.

2. С помощью этой команды найдите cmdlets резервного копирования Azure PowerShell:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Просмотрите псевдонимы и cmdlets для резервного копирования Azure и хранилища служб восстановления. Вот пример того, что вы можете увидеть. Это не полный список cmdlets.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Вопийте на свою учетную запись Azure с **помощью Connect-AzAccount**.
5. На веб-странице, которая появляется, вам предлагается ввести учетные данные учетной записи.

    * Кроме того, вы можете включить учетные данные учетной записи в качестве параметра в cmdlet **Connect-AzAccount** с **-Credential**.
    * Если вы являетесь партнером CSP, работающим на арендатора, укажите клиента в качестве арендатора, используя его основное доменное имя арендатора или арендатора. Например: **Connect-AzAccount-Tenant **fabrikam.com.

6. Связать подписку, которая вы хотите использовать с учетной записью, потому что учетная запись может иметь несколько подписок.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик Служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Убедитесь, что провайдеры успешно зарегистрировались:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. В выводе команды убедитесь, что **РегистрацияState** изменяется в **Зарегистрированный**. Если это не так, запустите **Регистр-AzResourceProvider** cmdlet снова.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища Служб восстановления

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

1. Хранилище помещается в группу ресурсов. Если у вас нет существующей группы ресурсов, создайте новую группу с [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) В этом примере мы создаем новую группу ресурсов в регионе Западного США.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Для создания хранилища используйте смдlet [New-AzRecoveryServicesVault.](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Укажите тип избыточности для использования для хранения хранилища.

    * [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md).
    * В следующем примере приводится опция **-BackupStorageRedundancy** для[Set-AzRecoveryServicesBackUpProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd для **тестового набора** **для GeoRedundant.**

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выход аналогичен следующему. Предоставляется связанная группа ресурсов и местоположение.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Задание контекста хранилища

Храните объект хранилища в переменной и установите контекст хранилища.

* Многие смлеты резервного копирования Azure требуют объекта хранилища служб восстановления в качестве ввода, поэтому удобно хранить объект хранилища в переменной.
* Контекст хранилища — это тип данных, защищаемых в хранилище. Установите его с [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). После установки контекста он применяется ко всем последующим cmdlets.

В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Получить идентификатор хранилища

Мы планируем устареть настройки контекста хранилища в соответствии с руководящими принципами Azure PowerShell. Вместо этого вы можете хранить или получать идентификатор хранилища и передавать его соответствующим командам следующим образом:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

Политика резервного копирования определяет расписание резервных upups и сроки резервного копирования моментов восстановления:

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* Просмотр сохранения политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackUpRetentionRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Просмотр графика политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackUpПланПланПланПланТ.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)
* Для создания новой политики резервного копирования используется [cmdlet New-AzRecoveryServicesBackUpProtectionPolicy.](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) Вы введали объекты политики расписания и удержания.

По умолчанию время начала определяется в объекте политики расписания. Используйте следующий пример, чтобы изменить время начала на нужное время начала. Нужное время начала должно быть в UTC также. Ниже приведен пример предполагает, что желаемое время начала 01:00 UTC для ежедневных резервных копирований.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Вы должны обеспечить время начала в 30 минуткратные кратные только. В приведенном выше примере это может быть только "01:00:00" или "02:30:00". Время начала не может быть "01:15:00"

В следующем примере показано сохранение политик расписания и хранения в переменных. Затем он использует эти переменные в качестве параметров для новой политики **(NewS'LPolicy**). **NewS'LPolicy** занимает ежедневное резервное копирование "Full", сохраняет его в течение 180 дней и занимает резервное копирование журнала каждые 2 часа

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выход аналогичен следующему.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Включение резервного копирования

### <a name="registering-the-sql-vm"></a>Регистрация ВМ СЗЛ

Для резервных отставаний От Azure VM и акций Azure File служба резервного копирования может подключиться к этим ресурсам Управления ресурсами Azure и получить соответствующие сведения. Поскольку S'L является приложением в Azure VM, служба резервного копирования нуждается в разрешении на доступ к приложению и получение необходимых деталей. Для этого необходимо *«зарегистрировать»* VM Azure, содержащее приложение S'L с хранилищем служб восстановления. После того, как вы зарегистрируете VM с хранилищем, вы сможете защитить DB для Этого хранилища только в этом хранилище. Для регистрации VM используйте [Регистр-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Команда вернет «контейнер резервного копирования» этого ресурса, и статус будет «зарегистрирован»

> [!NOTE]
> Если параметр силы не дан, пользователя просят подтвердить текстом :Хотите отключить защиту для этого контейнера». Пожалуйста, игнорируйте этот текст и сказать "Y", чтобы подтвердить. Это известная проблема, и мы работаем над удалением текста и требования к параметру силы.

### <a name="fetching-sql-dbs"></a>Получение S'L DB

Как только регистрация будет завершена, служба резервного копирования сможет перечислить все доступные компоненты S'L в VM. Для просмотра всех компонентов S'L, которые еще не были подкреплены в этом хранилище, используйте [Get-AzRecoveryServicesBackProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

На выходе будут отображаться все незащищенные компоненты S'L во всех VMs-серверах, зарегистрированных в этом хранилище с типом элемента и ServerName. Вы можете дополнительно отфильтровать к определенному VM S'L, передавая параметр «-контейнер» или использовать комбинацию «Имя» и «ServerName» вместе с флагом ItemType, чтобы прибыть к уникальному элементу S'L.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Настройка резервного копирования

Теперь, когда у нас есть необходимый S'L DB и политика, с которой он должен быть подкреплен, мы можем использовать [Enable-AzRecoveryServicesBackUpProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet для настройки резервной копии для этого S'L DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Команда ждет завершения резервного копирования и возвращает следующий вывод.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Получение новых DB СЗЛ

Как только машина зарегистрирована, служба резервного копирования будет получать сведения о DBs доступны тогда. Если пользователь добавляет экземпляры S'L DBs/S'L в зарегистрированную машину позже, необходимо вручную вызвать службу резервного копирования для выполнения свежего «запроса», чтобы получить ВСЕ незащищенные DB (включая недавно добавленные) снова. Для проведения нового запроса используйте [инициализировать-AzRecoveryServicesUpupUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet на S'L VM. Команда ждет завершения операции. Позже используйте [Get-AzRecoveryServicesBackProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet, чтобы получить список последних незащищенных компонентов S'L

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

После того, как соответствующие защищенные элементы извлечены, включить резервные материалы в соответствии с инструкциями в [вышеуказанном разделе.](#configuring-backup)
Если вы не хотите вручную обнаруживать новые DB, он может выбрать автозащиту, как показано [ниже.](#enable-autoprotection)

## <a name="enable-autoprotection"></a>Включить автозащиту

Пользователь может настроить резервную линию таким образом, что все DB, добавленные в будущем, автоматически защищены определенной политикой. Для обеспечения автозащиты используйте [Enable-AzRecoveryServicesBackUpAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

Так как инструкция состоит в резервном копировании всех будущих DB, операция выполняется на уровне S'LInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

После того, как намерение автозащиты дано, запрос в машину, чтобы получить вновь добавленные DBs происходит в качестве запланированной фоновой задачи каждые 8 часов.

## <a name="restore-sql-dbs"></a>Восстановление S'L DB

Резервное копирование Azure может восстановить базы данных сервера S'L Server, которые работают на ВМ Azure следующим образом:

* Восстановление определенной даты или времени (на второй) с помощью резервных копий журнала транзакций. Резервное копирование Azure автоматически определяет соответствующее полное резервное копирование дифференциала и цепочку резервных копий журнала, которые необходимы для восстановления на основе выбранного времени.
* Восстановление определенного полного или дифференциального резервного копирования для восстановления в определенной точке восстановления.

Перед восстановлением DB-серверов проверьте предпосылки, указанные [здесь.](restore-sql-database-azure-vm.md#prerequisites)

Сначала возьмите соответствующую резервную копию S'L DB с помощью [Get-AzRecoveryServicesBackUpUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Получить соответствующее время восстановления

Как указано выше, пользователь может восстановить резервное копирование S'L DB в полной/дифференцированных копиях **или** в точку входа в срок.

#### <a name="fetch-distinct-recovery-points"></a>Получить различные точки восстановления

Используйте [Get-AzRecoveryServicesBackUpRecoveryRecoveryPoint,](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) чтобы получить различные (полные/дифференцированные) точки восстановления для резервного копирования S'L DB.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Выход аналогичен приведению в следующем примере

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Используйте фильтр 'RecoveryPointId' или фильтр массива, чтобы получить соответствующую точку восстановления.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Точка восстановления в момент времени

Если пользователь хочет восстановить DB в определенной точке времени, используйте [Get-AzRecoveryServicesBackUpRecoveryRecoveryRecoveryLog.A.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) Cmdlet возвращает список дат, которые представляют время начала и окончания непрерывной непрерывной непрерывной цепочки журналов для этого элемента резервного копирования S'L. Пожеланная точка времени должна находиться в пределах этого диапазона.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Выход будет аналогичен следующему примеру.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Вышеуказанный вывод означает, что пользователь может восстановить любую точку времени между отображаемым временем начала и временем окончания. Времена в UTC. Постройте любую точку в времени в PS, которая находится в диапазоне, показанном выше.

> [!NOTE]
> При выбранном для восстановления времени входа в систему пользователю не нужно указывать отправную точку, т.е. полную резервную копию, из которой восстанавливается DB. Служба резервного копирования Azure позаботится обо всем плане восстановления, т.е. о том, какой полный резервный копий, какие резервные копии для журнала применять и т.д.

### <a name="determine-recovery-configuration"></a>Определение конфигурации восстановления

В случае восстановления S'L DB поддерживаются следующие сценарии восстановления.

* Переопределение резервного копирования S'L DB с данными из другой точки восстановления - OriginalWorkloadRestore
* Восстановление S'L DB в качестве нового DB в том же экземпляре S-L - AlternateWorkloadRestore
* Восстановление S'L DB в качестве нового DB в другом экземпляре S'L в другом экземпляре S'L VM - AlternateWorkloadRestore
* Восстановление S'L DB как файлы .bak -RestoreAsFiles

После получения соответствующей точки восстановления (отличительной или логин точки в срок), используйте [Get-AzRecoveryServicesBackUpupWorkloadLoad](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0)

#### <a name="original-workload-restore"></a>Восстановление исходной рабочей нагрузки

Чтобы переопределить резервный DB с данными из точки восстановления, просто укажите правильный флаг и соответствующую точку восстановления, как показано в следующем примере(ы).

##### <a name="original-restore-with-distinct-recovery-point"></a>Исходное восстановление с четкой точкой восстановления

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Оригинальная реставрация с помощью водоотведения

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Восстановление альтернативной рабочей нагрузки

> [!IMPORTANT]
> Резервное копирование S'L DB может быть восстановлено в качестве нового DB только для другого S'LInstance, в Azure VM "зарегистрирован" в этом хранилище.

Как указывалось выше, если целевая цель S'LInstance находится в другом Azure VM, убедитесь, что она зарегистрирована в [этом хранилище,](#registering-the-sql-vm) и соответствующий S'LInstance отображается в качестве защитимого элемента.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Затем просто пройдите соответствующую точку восстановления, нацеливай экземпляр S'L с правым флагом, как показано ниже.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Альтернативное восстановление с четкой точкой восстановления

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Альтернативное восстановление с помощью водоотведения

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Восстановление как файлы

Для восстановления данных резервного копирования в виде файлов .bak вместо базы данных выберите **опцию «Восстановление» в качестве файлов.** Резервное копирование S'L DB может быть восстановлено в любой целевой VM, которая зарегистрирована в этом хранилище.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Восстановление как файлы с четкой точкой восстановления

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Восстановление как файлы с журналом точка в срок от последних полных

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Восстановление как файлы с журналом point-in-time из указанного полного

Если вы хотите дать конкретный полный, который должен быть использован для восстановления, используйте следующую команду:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Объект конечной точки восстановления, полученный от [Get-AzRecoveryServicesBackUpWorkloadLoadRecoveryAnd.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet, имеет всю соответствующую информацию для восстановления и показан ниже.

```output
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
```

Вы можете отсеивать восстановленное имя DB, OverwriteWLIfpresent, NoRecoveryMode и targetPhysicalPath поля. Получите более подробную информацию о целевых путях файла, как показано ниже.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Установите соответствующие свойства PS в виде значений строки, как показано ниже.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
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
```

> [!IMPORTANT]
> Убедитесь, что конечный объект восстановления имеет все необходимые и надлежащие значения, так как операция восстановления будет основана на объекте конфигурации.

### <a name="restore-with-relevant-configuration"></a>Восстановление с соответствующей конфигурацией

После получения и проверки соответствующего объекта восстановления Config используйте смдлет [Restore-AzRecoveryServicesUpUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet для начала процесса восстановления.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Операция восстановления возвращает задание для отслеживания.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Управление резервными отсударями S'L

### <a name="on-demand-backup"></a>Резервное копирование по требованию

После включения резервного копирования для DB пользователь может также вызвать резервное копирование по требованию для DB с помощью [резервного копирования-AzRecoveryBackUpUpUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. Следующий пример запускает полную резервную поддержку на S'L DB с включенным сжатием и полное резервное копирование должно быть сохранено в течение 60 дней.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Команда резервного копирования по требованию возвращает задание для отслеживания.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Если результат потерян или вы хотите получить соответствующий идентификатор вакансий, [получите список заданий](#track-azure-backup-jobs) из службы резервного копирования Azure, а затем отследите его и его детали.

### <a name="change-policy-for-backup-items"></a>Изменение политики для элементов резервного копирования

Пользователь может либо изменить существующую политику, либо изменить политику резервного элемента с Policy1 на Policy2. Для переключения политик для резервного элемента, извлечения соответствующей политики и резервного копирования элемента и использовать команду [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) с элементом резервного копирования в качестве параметра.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Команда ждет завершения резервного копирования и возвращает следующий вывод.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Перерегистрация VMs-м

> [!WARNING]
> Убедитесь в том, чтобы прочитать этот [документ,](backup-sql-server-azure-troubleshoot.md#re-registration-failures) чтобы понять симптомы отказа и причины, прежде чем пытаться перерегистрации

Чтобы инициировать перерегистрацию ВМ, возьмите соответствующий резервный контейнер и передайте его в регистр cmdlet.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Остановить защиту

#### <a name="retain-data"></a>Сохранение данных

Если пользователь хочет прекратить защиту, он может использовать [Disable-AzRecoveryServicesBackProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet. Это остановит запланированное резервное копирование, но данные, резервные копии до сих пор сохраняется навсегда.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>удаление резервных копий;

Для того, чтобы полностью удалить сохраненные данные резервного копирования в хранилище, просто добавьте флаг '-RemoveRecoveryPoints' флаг/переключатель в [команду защиты 'отключаемых'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Отключить автоматическую защиту

Если автозащита была настроена на S'LInstance, пользователь может отключить ее с помощью [Disable-AzRecoveryServicesBackUpAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Нерегистрируйте S'L VM

Если все DB сервера S'L [больше не защищены и нет резервных данных,](#delete-backup-data)пользователь может отменить регистрацию VM из этого хранилища. Только тогда пользователь может защитить DB в другом хранилище. Используйте [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet для регистрации S'L VM.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Отслеживайте задания резервного копирования Azure

Важно отметить, что резервное копирование Azure отслеживает только задания пользователей, срабатывающие в резервном копировании S'L. Запланированные резервные копии (включая резервные копии журнала) не видны в портале/powershell. Однако, если какие-либо запланированные задания не удались, [резервное оповещение](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) генерируется и отображается на портале. [Используйте Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) для отслеживания всех запланированных заданий и другой соответствующей информации.

Пользователи могут отслеживать операции по запросу/пользователя с помощью JobID, который возвращается в [выводе](#on-demand-backup) асинхронных заданий, таких как резервное копирование. Используйте [Get-AzRecoveryServicesBackupJobJobJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet для отслеживания задания и ее деталей.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Чтобы получить список заданий по требованию и их статусы из службы резервного копирования Azure, воспользуйтесь [Get-AzRecoveryServicesBackUpUpJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet. Следующий пример возвращает все незавершенные задания S'L.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Чтобы отменить незавершенное задание, используйте [Stop-AzRecoveryServicesBackUpJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>Управление группами S'L всегда на наличие

Для групп доступности S'L Always On Availability обязательно [зарегистрируйте все узлы](#registering-the-sql-vm) группы доступности (AG). После регистрации всех узлов объект группы доступности S'L логически создается под защищенными элементами. Базы данных в соответствии с S'L AG будут перечислены как «S'LDatabase». Узлы будут отображаться как автономные экземпляры, а базы данных по умолчанию s-L под ними будут также перечислены в качестве баз данных S'L.

Например, предположим, что у Компании Есть два узла: «sql-сервер-0» и «sql-сервер-1» и 1 S'L AG DB. После регистрации обоих узлов, если пользователь [перечисляет защищенные элементы,](#fetching-sql-dbs)в нем перечислены следующие компоненты

* Объект S'L AG - защищенный тип элемента в качестве группы S'LAvailabilityGroup
* DB - защищенный тип элемента в качестве S'LDatabase
* sql-сервер-0 - защищенный тип элемента как S'LInstance
* sql-сервер-1 - защищенный тип элемента как S'LInstance
* Любые DB по умолчанию (мастер, модель, msdb) под sql-сервер-0 - защищенный тип элемента, как S'LDatabase
* Любые DB по умолчанию (мастер, модель, msdb) под sql-сервером-1 - защищенный тип элемента, как S'LDatabase

sql-сервер-0, sql-сервер-1 также будет указан как "AzureVMAppContainer" при [перечислении резервных контейнеров.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)

Просто возьмите соответствующую базу данных S'L, чтобы [включить резервную и](#configuring-backup) [по требованию резервное копирование](#on-demand-backup) и [восстановление PS cmdlets](#restore-sql-dbs) идентичны.
