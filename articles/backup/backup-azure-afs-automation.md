---
title: Резервное копирование файлов Azure с помощью PowerShell
description: Из этой статьи вы узнаете, как выполнять резервное копирование файлов Azure с помощью службы Azure Backup и PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 865cfc6daa7568236b0306ba591b42a9f7704dd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101184"
---
# <a name="back-up-azure-files-with-powershell"></a>Резервное копирование файлов Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования файлового ресурса службы файлов Azure с помощью хранилища служб восстановления [Azure Backup](backup-overview.md) .

В статье описывается выполнение следующих задач:

> [!div class="checklist"]
>
> * Настройте PowerShell и зарегистрируйте поставщик служб восстановления Azure.
> * Создайте хранилище служб восстановления,
> * Настройте резервное копирование для файлового ресурса Azure.
> * Запустите задание резервного копирования.

## <a name="before-you-start"></a>Перед началом работы

* Дополнительные [сведения](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Проверьте иерархию объектов PowerShell для служб восстановления.

## <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Иерархия объектов представлена на следующей схеме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Ознакомьтесь со справочником по [командлету](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** в библиотеке Azure.

## <a name="set-up-and-install"></a>Установка и установка

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Настройте PowerShell следующим образом.

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.0.0.

    > [!WARNING]
    > Минимальная версия PS, необходимая для резервного копирования файлового ресурса Azure, — **AZ. RecoveryServices 2.6.0**. Обновите версию, чтобы избежать проблем с существующими скриптами. Установите минимальную версию с помощью следующей команды PS:

    ```powershell
    Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    ```

2. Найдите командлеты PowerShell Azure Backup с помощью следующей команды:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Проверьте псевдонимы и командлеты для Azure Backup, Azure Site Recovery и хранилища служб восстановления. Ниже приведен пример того, что можно увидеть. Это не полный список командлетов.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Войдите в учетную запись Azure с помощью **Connect-азаккаунт**.
5. На появившейся веб-странице вам будет предложено ввести учетные данные вашей учетной записи.

    * Кроме того, можно включить учетные данные учетной записи в качестве параметра в командлет **Connect-азаккаунт** с параметром **-Credential**.
    * Если вы являетесь партнером CSP, работающим от имени клиента, укажите клиента в качестве клиента, используя его идентификатор tenantID или основное доменное имя клиента. Например: **Connect-AzAccount-Tenant **fabrikam.com.

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

## <a name="create-a-recovery-services-vault"></a>Создание хранилища Служб восстановления

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

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Важное замечание. Идентификация элемента резервного копирования для резервного копирования AFS

В этом разделе приводится важное изменение в резервной копии AFS при подготовке к работе в общедоступной версии.

При включении резервного копирования для AFS пользователь предоставляет понятное имя общей папки клиента в качестве имени сущности и созданного элемента резервного копирования. Имя элемента резервной копии — это уникальный идентификатор, созданный службой Azure Backup. Обычно идентификатор включает понятное имя пользователя. Но для решения важного сценария обратимого удаления, в котором можно удалить файловый ресурс и создать другую общую папку с тем же именем, уникальный идентификатор файлового ресурса Azure теперь будет ИДЕНТИФИКАТОРом, а не понятным именем клиента. Чтобы узнать уникальный идентификатор или имя каждого элемента, просто выполните ```Get-AzRecoveryServicesBackupItem``` команду с соответствующими фильтрами для Баккупманажементтипе и ворклоадтипе, чтобы получить все соответствующие элементы, а затем просмотрите поле Name в возвращенном объекте или ответе PS. Всегда рекомендуется выводить список элементов, а затем получать их уникальные имена из поля "имя" в ответе. Используйте это значение для фильтрации элементов с помощью параметра "Name". В противном случае используйте параметр FriendlyName для получения элемента с понятным именем или идентификатором клиента.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для "az. RecoveryServices 2.6.0" для резервных копий AFS. В этой версии фильтр "friendlyName" доступен для ```Get-AzRecoveryServicesBackupItem``` команды. Передайте имя общего файлового ресурса Azure в параметр friendlyName. Если имя файлового ресурса Azure передается в параметр "имя", эта версия выдает предупреждение, чтобы передать это понятное имя в параметр понятного имени. Установка этой минимальной версии может привести к сбою существующих сценариев. Установите минимальную версию PS с помощью следующей команды.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Активирование резервного копирования по запросу

Используйте [BACKUP-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) для запуска резервного копирования по запросу для защищенного файлового ресурса Azure.

1. Получите учетную запись хранения из контейнера в хранилище, в котором хранятся резервные копии данных, с помощью [Get-азрековерисервицесбаккупконтаинер](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Чтобы запустить задание резервного копирования, получите сведения о файловом ресурсе Azure с помощью [Get-азрековерисервицесбаккупитем](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Выполните резервное копирование по требованию с помощью командлета [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Выполните резервное копирование по запросу следующим образом:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Мы используем моментальные снимки общего ресурса файлов Azure при создании резервных копий и поэтому задание, как правило, завершается к тому времени, когда команда возвращает выходные данные.

### <a name="using-a-runbook-to-schedule-backups"></a>Создание расписания резервного копирования с помощью модуля Runbook

Если вы ищете примеры скриптов, см. [Пример скрипта в GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) с помощью модуля Runbook службы автоматизации Azure.

>[!NOTE]
> Политика резервного копирования файловых ресурсов Azure теперь поддерживает настройку резервного копирования с ежедневными, еженедельными, ежемесячными и ежегодными сроками хранения.

## <a name="next-steps"></a>Дальнейшие шаги

[Сведения о](backup-afs.md) резервном копировании файлов Azure в портал Azure.
