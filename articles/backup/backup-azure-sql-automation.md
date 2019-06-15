---
title: 'Azure Backup: Резервное копирование и восстановление баз данных SQL на виртуальных машинах Azure с помощью службы архивации Azure и PowerShell'
description: Резервное копирование и восстановление баз данных SQL на виртуальных машинах Azure с помощью службы архивации Azure и PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Служба архивации Azure; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734226"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Резервное копирование и восстановление баз данных SQL на виртуальных машинах Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования и восстановления базы данных SQL в виртуальной Машине Azure с помощью [Azure Backup](backup-overview.md) хранилище служб восстановления.

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
> * Настроить PowerShell и зарегистрировать поставщик служб восстановления Azure.
> * Создайте хранилище служб восстановления,
> * Настройка резервного копирования для базы данных SQL на виртуальной Машине Azure.
> * Запустите задание резервного копирования.
> * Восстановление резервных копий базы данных SQL.
> * Мониторинг резервного копирования и восстановления заданий.

## <a name="before-you-start"></a>Перед началом работы

* [Дополнительные сведения](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Ознакомьтесь с функциональными возможностями для [резервное копирование баз данных SQL в виртуальных машинах Azure](backup-azure-sql-database.md#before-you-start).
* Просмотр иерархии объектов PowerShell для служб восстановления.

### <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Объект иерархию на следующей схеме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Просмотрите **Az.RecoveryServices** [Справочник по командлетам](/powershell/module/az.recoveryservices) ссылку в библиотеке Azure.

### <a name="set-up-and-install"></a>Настройка и установка

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Настройте PowerShell следующим образом:

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.5.0.

2. Найти командлеты Azure Backup PowerShell с помощью следующей команды:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Просмотрите псевдонимов и командлетов для Azure Backup и хранилище служб восстановления. Вот что можно увидеть пример. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Войдите в учетную запись Azure с помощью **Connect AzAccount**.
5. На появившейся веб-странице будет предложено ввести учетные данные учетной записи.

    * Кроме того, можно добавить учетные данные учетной записи в качестве параметра **Connect AzAccount** командлет с **-Credential**.
    * Если вы являетесь партнером CSP, работа для клиента, укажите заказчика в качестве клиента, с помощью их основное доменное имя клиента или идентификатор клиента. Например: **Connect-AzAccount-Tenant** fabrikam.com.

6. Свяжите подписку, которую вы хотите использовать с учетной записью, так как учетной записи может быть несколько подписок.

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

9. Убедитесь, что в результатах выполнения команды **RegistrationState** примет **зарегистрированные**. Если он не запускается **Register AzResourceProvider** командлет еще раз.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

1. Хранилище будет помещено в группу ресурсов. Если у вас нет существующей группы ресурсов, создайте новую с [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). В этом примере мы создадим новую группу ресурсов в западной части США.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Используйте [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) командлет, чтобы создать хранилище. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Укажите тип избыточности для хранилища хранилище.

    * [локально избыточное](../storage/common/storage-redundancy-lrs.md) или [геоизбыточное](../storage/common/storage-redundancy-grs.md).
    * В следующем примере задается **- BackupStorageRedundancy** для параметра[набора AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd для **testvault** присвоено  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выходные данные следующего вида. Связанная группа ресурсов и расположение предоставляются.

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

* Многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входного, поэтому его можно хранить объект хранилища в переменной.
* Контекст хранилища — это тип данных, защищаемых в хранилище. Установка его с помощью [AzRecoveryServicesVaultContext набора](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Контекст равен, он применяется ко всем последующим командлетам.

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

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* Представление хранения политику резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Представление расписания политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Использовании [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) командлет, чтобы создать новую политику резервного копирования. Введенные объекты политик расписания и хранения.

В следующем примере показано сохранение политик расписания и хранения в переменных. Он затем эти переменные используются в качестве параметров для новой политики (**NewSQLPolicy**). **NewSQLPolicy** принимает резервного копирования ежедневно «Full», хранит их в течение 180 дней и создает резервную копию журналов каждые 2 часа

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выходные данные следующего вида.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Включение резервного копирования

### <a name="registering-the-sql-vm"></a>Регистрация виртуальной Машины SQL

Для архивации виртуальных Машин Azure и файловые ресурсы Azure службы резервного копирования можно подключиться к этим ресурсам Azure Resource Manager и получить соответствующие данные. Так как SQL — это приложение на виртуальной Машине Azure, служба архивации необходимо предоставить разрешение на доступ к приложению и получить необходимые сведения. Чтобы сделать это, необходимо *«зарегистрироваться»* виртуальной Машине Azure, содержащий приложение SQL в хранилище служб восстановления. После регистрации виртуальной Машины SQL в хранилище, вы можете защитить баз данных SQL в только в этом хранилище. Используйте [Register AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) командлет PS, чтобы зарегистрировать виртуальную Машину.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Команда вернет «резервного копирования контейнер» этого ресурса и состояние будет «зарегистрировано»

> [!NOTE]
> Если параметр force не задан, пользователю предлагается подтвердить с помощью текстового сообщения «вы хотите отключить защиту для этого контейнера». Игнорировать этот текст и сказать «Y» для подтверждения. Это известная проблема, и мы работаем над текстом и требования для параметра force

### <a name="fetching-sql-dbs"></a>Получение баз данных SQL

По окончании регистрации службы резервного копирования будут иметь возможность список всех доступных компонентов SQL на виртуальной Машине. Чтобы просмотреть все компоненты SQL, но для резервного копирования в хранилище, используйте [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) командлета PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Все компоненты незащищенные SQL будут показаны результаты для всех виртуальных машин SQL, зарегистрированного в этом хранилище с типом элемента и ServerName. Вы можете дополнительно фильтровать в определенную виртуальную машину SQL, передав "-контейнер" параметр или используйте сочетание «Name» и «Имя_сервера», а также ItemType флаг можно перейти на уникальный элемент SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Настройка резервного копирования

Теперь, когда у нас есть необходимые базы данных SQL и политику с помощью которого он требуется для резервного копирования, мы можем [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) командлет для настройки резервного копирования для этой базы данных SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Команда ожидает, пока завершится Настройка архивации и возвращает следующие выходные данные.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Получение новых баз данных SQL

После регистрации компьютера службы резервного копирования будет получения сведений о баз данных SQL, затем доступны. Если пользователь добавляет экземпляры баз данных SQL или SQL для зарегистрированной машины более поздней версии, необходимо вручную запустить службу резервного копирования для выполнения новый «запрос» для получения всех незащищенных баз данных SQL (включая вновь добавленный) еще раз. Используйте [Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) командлета PS на виртуальной Машине SQL для выполнения новых запросов. Команда ожидает завершения операции. В дальнейшем использовать [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) командлета PS, чтобы получить список последних незащищенные компоненты SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Как только будут выбраны соответствующие защищаемых элементов, включить резервные копии, как описано в статье [над разделом](#configuring-backup).
Если один не нужно вручную обнаружить новых баз данных, они могут использовать autoprotection описанных [ниже](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Включить AutoProtection

Таким образом, что добавлена в будущем всех баз данных SQL автоматически защищены с помощью определенных политик, пользователь может настроить резервное копирование. Чтобы включить autoprotection, используйте [Enable AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) командлета PS.

Так как инструкция является резервное копирование всех будущих баз данных SQL, операция выполняется по SQLInstance уровня.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Цель autoprotection предоставляется, запрос на машину, чтобы получить только что добавленных баз данных SQL происходит как запланированные фоновая задача каждые 8 часов.

## <a name="restore-sql-dbs"></a>Восстановление баз данных SQL

Служба архивации Azure можно восстановить базы данных SQL Server, работающие на виртуальных машинах Azure, следующим образом:

1. Восстановите определенную дату или время (в секунду) с помощью резервных копий журналов транзакций. Azure Backup автоматически определяет соответствующие полное разностное резервное копирование и цепочку резервных копий журнала, которые необходимы для восстановления на основе выбранного времени.
2. Восстановите определенную полную или разностную резервную копию восстановить до определенной точки восстановления.

Проверка предварительных требований упоминалось [здесь](restore-sql-database-azure-vm.md#prerequisites) перед восстановлением баз данных SQL.

Сначала получить соответствующий резервную копию базы данных SQL с помощью [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) командлета PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Время восстановления соответствующие извлечения

Как описано выше, пользователь мог восстановить резервные копии базы данных SQL на полную или разностную копию **или** для журнала в определенный момент времени.

#### <a name="fetch-distinct-recovery-points"></a>Получить точки восстановления distinct

Используйте [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) получить различные точки восстановления (полной или разностной) для резервных копий базы данных SQL.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Результат, аналогичный приведенному ниже

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Точка восстановления соответствующие используйте «RecoveryPointId» фильтр или фильтр массива.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Получить точку восстановления на момент времени

Если пользователь хочет восстановить базы данных для определенных в определенный момент времени, используйте [Get AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) командлета PS. Командлет возвращает список дат, которые представляют время начала и окончания журнала непрерывной, непрерывной цепочки для этого элемента архивации SQL. Требуемый в определенный момент времени должны находиться в этом диапазоне.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Результат должен быть аналогичен приведенному ниже.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Выходные данные выше означает, что этот пользователь можно восстановить до любой точки времени между время отображается начала и время окончания. Значения времени указаны в формате UTC. Создайте любой в определенный момент времени в Powershell, который находится в пределах диапазона, показанный выше.

> [!NOTE]
> Когда журнала в определенный момент времени для восстановления, пользователю не требуется указывать, т. е. начальная точка, полной резервной копии, из которого восстанавливается база данных. Служба Azure Backup позаботится о плане восстановления всей т. е. который полного резервного копирования на выбор данных, резервные копии журналов для применения д.

### <a name="determine-recovery-configuration"></a>Определить конфигурацию восстановления

В случае восстановление базы данных SQL поддерживаются следующие сценарии восстановления.

1. Переопределение резервных копий базы данных SQL с данными из другой точки восстановления — OriginalWorkloadRestore
2. Восстановление базы данных SQL в качестве новой базы данных на том же экземпляре SQL - AlternateWorkloadRestore
3. Восстановление базы данных SQL в качестве новой базы данных в другой экземпляр SQL на другой виртуальной Машине SQL - AlternateWorkloadRestore

После получение точки восстановления, соответствующих (distinct или журнала в определенный момент времени), используйте [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) командлета PS для получения объекта конфигурации восстановления согласно плана восстановления нужный.

#### <a name="original-workload-restore"></a>Исходное восстановление рабочей нагрузки

Чтобы переопределить резервную копию базы данных с данными из точки восстановления, просто укажите флаг справа и точку восстановления, соответствующие как показано в следующем запроса(-ов).

##### <a name="original-restore-with-distinct-recovery-point"></a>Исходное восстановление с помощью различных точки восстановления

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Исходное восстановление с помощью журнала в определенный момент времени

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Восстановление альтернативного рабочей нагрузки

> [!IMPORTANT]
> Резервные копии базы данных SQL могут быть восстановлены как новая база данных для другой SQLInstance только на виртуальной Машине Azure, «зарегистрировано», в этом хранилище.

Как описано выше, если они вызваны SQLInstance целевой объект в другой виртуальной Машине Azure, убедитесь, что это [регистрации в этом хранилище](#registering-the-sql-vm) и соответствующие SQLInstance отображается как защищаемый элемент.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Затем просто передайте точку восстановления, соответствующих, целевом экземпляре SQL Server с флагом правой как показано ниже.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Альтернативный восстановление с помощью различных точки восстановления

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Альтернативный восстановление с помощью журнала в определенный момент времени

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Получить объект конфигурации точки конечный объект восстановления из [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) командлета PS имеет все необходимые сведения для восстановления и как показано ниже.

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

Можно изменить восстановленной базы данных имя, OverwriteWLIfpresent, NoRecoveryMode и targetPhysicalPath поля. Получения дополнительных сведений о пути к целевым файлам, как показано ниже.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Установите соответствующие свойства PS в виде строковых значений, как показано ниже.

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
> Убедитесь, что объект конфигурации конечный объект восстановления имеет все необходимые и соответствующие значения, так как операция восстановления будет основываться на объект конфигурации.

### <a name="restore-with-relevant-configuration"></a>Восстановление с помощью конфигураций, связанных с

После объекта конфигурации соответствующие восстановления получить и проверить, используйте [AzRecoveryServicesBackupItem восстановления](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) командлета PS, чтобы начать процесс восстановления.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Операция восстановления возвращает задание для отслеживания.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Управление резервными копиями SQL

### <a name="on-demand-backup"></a>Резервное копирование по запросу

После включения резервного копирования для базы данных, пользователь может также инициировать резервное копирование по запросу для базы данных с помощью [AzRecoveryServicesBackupItem резервного копирования](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) командлета PS. Следующий пример запускает полное резервное копирование базы данных SQL с включенным сжатием данных и полной резервной копии должны храниться в течение 60 дней.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Команды резервного копирования adhoc возвращает задание для отслеживания.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Если выходные данные потеряны или если вы хотите получить идентификатор соответствующего задания [получить список заданий](#track-azure-backup-jobs) из резервной копии Azure службы и затем отслеживать его и сведения о нем.

### <a name="change-policy-for-backup-items"></a>Изменение политики для архивных элементов

Пользователя можно изменить существующую политику или изменить политику элемента резервных копий с политика 1 на политика 2. Для переключения политики для резервных копий элемента, просто получить нужную политику и резервное копирование элемента и использовать [Enable AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) с архивного элемента в качестве параметра.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Команда ожидает, пока завершится Настройка архивации и возвращает следующие выходные данные.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Повторно зарегистрируйте виртуальные машины SQL

> [!WARNING]
> Не забудьте прочитать этот текст [документа](backup-sql-server-azure-troubleshoot.md#re-registration-failures) для понимания ошибок и причины перед попыткой повторной регистрации

Чтобы активировать повторную регистрацию виртуальной машины SQL, получить соответствующие контейнер резервного копирования и передайте его в командлет register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>остановка защиты;

#### <a name="retain-data"></a>Сохранение данных

Если пользователь хочет остановить защиту, они могут использовать [Disable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) командлета PS. Это приведет к остановке плановых резервных копий, но данные резервного копирования до теперь сохраняется навсегда.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>удаление резервных копий;

Чтобы полностью удалить данные резервных копий хранятся в хранилище, просто добавьте "-RemoveRecoveryPoints флаг/переключиться в режим [защиты команду «Отключить»](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Отключить автоматическую защиту

Если autoprotection был настроен на SQLInstance, пользователь может отключить его с помощью [Disable AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) командлета PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Отмена регистрации виртуальной Машины SQL

Если все базы данных SQL Server, [являются больше не существует защищенных и нет резервной копии данных](#delete-backup-data), можно отменить регистрацию виртуальной Машины SQL из этого хранилища. Только после этого пользователя позволяет защищать баз данных в другом хранилище. Используйте [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) командлета PS, чтобы отменить регистрацию виртуальной Машины SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Отслеживания заданий Azure

Важно отметить, что служба архивации Azure отслеживает только задания активации пользователя в резервной копии SQL. Архивацию по расписанию (включая резервные копии журналов), не отображаются в портала или powershell. Тем не менее, если какой-либо запланированные задания завершаться сбоем, [оповещения резервного копирования](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) создается и отображается на портале. [Используйте Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) для отслеживания всех запланированных заданий и другие важные сведения.

Пользователи могут отслеживать операции активации компьютер-компьютер или пользователя с JobID, который возвращается в [вывода](#on-demand-backup) асинхронных заданий, таких как резервное копирование. Используйте [Get AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) командлет PS, чтобы отслеживать задания и сведения о нем.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Чтобы получить список ad-hoc-заданий и их состояния из службы архивации Azure, используйте [Get AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) командлета PS. В следующем примере возвращается все выполняющиеся задания SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Чтобы отменить выполняющееся задание, используйте [Stop AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) командлета PS.

## <a name="managing-sql-always-on-availability-groups"></a>Управление группами доступности SQL AlwaysOn

Для SQL групп доступности AlwaysOn, убедитесь, что [регистрация всех узлов](#registering-the-sql-vm) группы доступности (AG). По окончании регистрации для всех узлов в объекте группы доступности SQL логически создается в защищаемых элементов. База данных SQL AG будет иметь статус «SQLDatabase». Узлы будут отображаться как автономные экземпляры и базы данных SQL по умолчанию, их будет иметь статус также баз данных SQL.

Например, предположим, SQL AG имеет два узла: «sql-server-0"и «sql-server-1" и 1 группы Доступности базы данных SQL. После регистрации оба этих узла, если пользователь [список защищаемых элементов](#fetching-sql-dbs), он содержит следующие компоненты

1. Объект группы Доступности SQL - защищаемый тип как SQLAvailabilityGroup элемента
2. SQL AG база данных — тип защищаемый элемент как SQLDatabase
3. SQL-server-0 - защищаемый элемент введите SQLInstance
4. SQL-server-1 - введите защищаемый элемент в качестве SQLInstance
5. Любое по умолчанию (master, model, msdb) баз данных SQL в sql-server-0 - защищаемый элемент введите SQLDatabase
6. Любое по умолчанию (master, model, msdb) баз данных SQL в sql-server-1 - введите защищаемый элемент в качестве SQLDatabase

SQL-server-0, sql server 1 будет также иметь статус «AzureVMAppContainer» при [резервного копирования контейнеры перечислены](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Просто получить соответствующую базу данных SQL для [включить резервное копирование](#configuring-backup) и [нерегламентированном резервном копировании](#on-demand-backup) и [восстановить командлеты PS](#restore-sql-dbs) идентичны.
