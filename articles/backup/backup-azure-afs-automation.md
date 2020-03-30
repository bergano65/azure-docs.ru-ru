---
title: Резервное копирование файлов Azure с помощью PowerShell
description: В этой статье узнайте, как создать резервную связь с помощью резервного копирования Azure и PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273544"
---
# <a name="back-up-azure-files-with-powershell"></a>Резервное копирование файлов Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для резервного копирования файла Azure Files с помощью хранилища служб восстановления [резервного копирования Azure.](backup-overview.md)

В статье описывается выполнение следующих задач:

> [!div class="checklist"]
>
> * Навлажь PowerShell и зарегистрируйте поставщика услуг восстановления Azure.
> * Создайте хранилище служб восстановления,
> * Нанастройка резервного копирования для общего файла Azure.
> * Выполнить работу резервного копирования.

## <a name="before-you-start"></a>Перед началом работы

* [Узнайте больше](backup-azure-recovery-services-vault-overview.md) о хранилищах служб восстановления.
* Прочитайте о возможностях предварительного просмотра для [резервного копирования акций файлов Azure](backup-afs.md).
* Просмотрите иерархию объектов PowerShell для служб восстановления.

## <a name="recovery-services-object-hierarchy"></a>Иерархия объектов служб восстановления

Иерархия объектов обобщена в следующей диаграмме.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Просмотрите ссылку **на ссылку Az.RecoveryServices** [cmdlet](/powershell/module/az.recoveryservices) в библиотеке Azure.

## <a name="set-up-and-install"></a>Настройка и установка

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Настройка PowerShell следующим образом:

1. [Скачайте последнюю версию Az PowerShell](/powershell/azure/install-az-ps). Минимальная требуемая версия — 1.0.0.

> [!WARNING]
> Минимальная версия PS, необходимая для предварительного просмотра, была 'Az 1.0.0'. В связи с предстоящими изменениями для GA, минимальная версия PS требуется будет 'Az.RecoveryServices 2.6.0'. Очень важно обновить все существующие версии PS до этой версии. В противном случае существующие скрипты будут ломаться после GA. Установите минимальную версию со следующими командами PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. С помощью этой команды найдите cmdlets резервного копирования Azure PowerShell:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Просмотрите псевдонимы и cmdlets для резервного копирования Azure, восстановления сайта Azure и хранилища служб восстановления. Вот пример того, что вы можете увидеть. Это не полный список cmdlets.

    ![Список командлетов Служб восстановления](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Вопийте на свою учетную запись Azure с **помощью Connect-AzAccount**.
5. На веб-странице, которая появляется, вам предлагается ввести учетные данные учетной записи.

    * Кроме того, вы можете включить учетные данные учетной записи в качестве параметра в cmdlet **Connect-AzAccount** с **-Credential**.
    * Если вы являетесь партнером CSP, работающим от имени арендатора, укажите клиента в качестве арендатора, используя его основное доменное имя арендатора или арендатора. Например: **Connect-AzAccount-Tenant **fabrikam.com.

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

Хранилище Служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **New-AzResourceGroup**. При создании группы ресурсов укажите ее имя и расположение.

Чтобы создать хранилище Служб восстановления, выполните описанные ниже действия.

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
   * В следующем примере приводится опция **-BackupStorageRedundancy** для[Set-AzRecoveryServicesBackupPropertiesProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd для **тестового набора** **для GeoRedundant.**

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Выполнив командлет [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

Выход аналогичен следующему. Обратите внимание, что предоставлена связанная группа ресурсов и местоположение.

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

Храните объект хранилища в переменной и установите контекст хранилища.

* Многие смлеты резервного копирования Azure требуют объекта хранилища служб восстановления в качестве ввода, поэтому удобно хранить объект хранилища в переменной.
* Контекст хранилища — это тип данных, защищаемых в хранилище. Установите его с [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). После установки контекста он применяется ко всем последующим cmdlets.

В следующем примере задается контекст для хранилища **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Получить идентификатор хранилища

Мы планируем устареть настройки контекста хранилища в соответствии с руководящими принципами Azure PowerShell. Вместо этого вы можете хранить или получать идентификатор хранилища и передавать его соответствующим командам. Итак, если вы еще не установили контекст хранилища или хотите указать команду для выполнения определенного хранилища, передайте идентификатор хранилища как "-vaultID" всем соответствующим командам следующим образом:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Настройка политики резервного копирования

Политика резервного копирования определяет расписание резервных upups и сроки резервного копирования моментов восстановления:

* Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления до ее удаления.
* Просмотр сохранения политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackUpRetentionRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Просмотр графика политики резервного копирования по умолчанию с помощью [Get-AzRecoveryServicesBackUpПланПланПланПланТ.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)
* Для создания новой политики резервного копирования используется [cmdlet New-AzRecoveryServicesBackUpProtectionPolicy.](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) Вы введали объекты политики расписания и удержания.

По умолчанию время начала определяется в объекте политики расписания. Используйте следующий пример, чтобы изменить время начала на нужное время начала. Нужное время начала должно быть в UTC также. Ниже приведен пример предполагает, что желаемое время начала 01:00 UTC для ежедневных резервных копирований.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Вы должны обеспечить время начала в 30 минуткратные кратные только. В приведенном выше примере это может быть только "01:00:00" или "02:30:00". Время начала не может быть "01:15:00"

В следующем примере показано сохранение политик расписания и хранения в переменных. Затем он использует эти переменные в качестве параметров для новой**политики (NewAFSPolicy**). **NewAFSPolicy** выполняет ежедневное резервное копирование и хранит его в течение 30 дней.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Выход аналогичен следующему.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Включение резервного копирования

После определения политики резервного копирования можно включить защиту файла Azure с помощью политики.

### <a name="retrieve-a-backup-policy"></a>Извлечение политики резервного копирования

Вы получаете соответствующий объект политики с [помощью Get-AzRecoveryServicesBackUpProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Используйте этот cmdlet для получения определенной политики или для просмотра политик, связанных с типом рабочей нагрузки.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Извлечение политики для типа рабочей нагрузки

В следующем примере приводится поиск политик для типа рабочей нагрузки **AzureFiles.**

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Выход аналогичен следующему.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Часовой пояс поля **BackupTime** в PowerShell — время в формате UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.

### <a name="retrieve-a-specific-policy"></a>Извлечение определенной политики

Следующая политика извлекает политику резервного копирования с именем **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Включить резервную и применять политику

Включить защиту с [помощью Enable-AzRecoveryServicesBackUpProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). После того, как политика связана с хранилищем, резервные ожидания срабатывают в соответствии с графиком политики.

Следующий пример позволяет защитить для теста совместного файла **AzureFileShare** в тесте учетной записи **храненияStorageAcct,** с **ежедневными политиками.**

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Команда ожидает завершения задания настройки защиты и предоставляет результаты, подобные приведенным ниже.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Важное уведомление - идентификация элементов резервного копирования для резервных upups AFS

В этом разделе излагаются важные изменения в резервной копировании AFS в рамках подготовки к GA.

В то время как включение резервного копирования для AFS, пользователь поставляет дружественному клиенту имя общего файла, поскольку создается имя сущности и элемент резервного копирования. Имя элемента резервного копирования — уникальный идентификатор, созданный службой резервного копирования Azure. Обычно идентификатор включает в себя удобное для пользователя имя. Но для обработки важного сценария мягкого удаления, когда доля файлов может быть удалена, а другой файл может быть создан с тем же именем, уникальная идентификация раздела файла Azure теперь будет идентификатором, а не дружественным к клиенту именем. Для того, чтобы узнать уникальную идентификацию/имя каждого элемента, просто запустите ```Get-AzRecoveryServicesBackupItem``` команду с соответствующими фильтрами для резервного копированияManagementType и WorkloadType, чтобы получить все соответствующие элементы, а затем наблюдать поле имен в возвращенном объекте PS/ответе. В ответ всегда рекомендуется перечислять элементы, а затем извлекать их уникальное название из поля «имя». Используйте это значение для фильтрации элементов с параметром "Имя". В противном случае используйте параметр FriendlyName для получения товара с его дружественным клиентом имя / идентификатор.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для 'Az.RecoveryServices 2.6.0' для резервных upups AFS. С этой версией для ```Get-AzRecoveryServicesBackupItem``` команды доступен фильтр 'friendlyName'. Передайте имя раздела файла Azure параметру friendlyName. Если вы передаете имя Azure File Share параметру «Имя», эта версия выбрасывает предупреждение о том, чтобы передать это дружественное имя дружественному параметру имени. Неустановка этой минимальной версии может привести к сбою существующих скриптов. Установите минимальную версию PS со следующей командой.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Активирование резервного копирования по запросу

Используйте [резервное копирование-AzRecoveryServicesBackupUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) для запуска резервного копирования по требованию для защищенной доли файлов Azure.

1. Извлеките учетную запись хранилища из контейнера в хранилище, в хранилище которого хранятся данные резервного копирования с [помощью Get-AzRecoveryServicesBackUpContainer.](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)
2. Чтобы начать работу резервного копирования, вы получаете информацию об общих долях файла Azure в [Get-AzRecoveryServicesBackupItem.](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)
3. Выполните резервное копирование по требованию с помощью командлета [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Запустите резервную по требованию резервную часть следующим образом:

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

### <a name="using-on-demand-backups-to-extend-retention"></a>Использование резервных upups по требованию для расширения удержания

Резервное копирование по требованию может быть использовано для сохранения снимков в течение 10 лет. Планировщики могут использоваться для выполнения скриптов PowerShell по требованию с выбранным удержанием и, таким образом, делать снимки через регулярные промежутки времени каждую неделю, месяц или год. Делая регулярные снимки, обратитесь к [ограничениям резервных отправления по требованию](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) с помощью резервного копирования Azure.

Если вы ищете примерскрипторов скриптов, вы можете<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>обратиться к образцу скрипта на GitHub ( ) с помощью runbook Azure Automation, который позволяет планировать резервные отжимы на периодической основе и сохранять их даже до 10 лет.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для 'Az.RecoveryServices 2.6.0' для резервных копирований AFS в ваших учебниках автоматизации. Вам придется заменить старый модуль «AzureRM» на модуль «Аз». С этой версией для ```Get-AzRecoveryServicesBackupItem``` команды доступен фильтр 'friendlyName'. Передайте имя общего файла azure параметру friendlyName. Если вы передаете имя раздела лазурного файла параметру «Имя», эта версия выбрасывает предупреждение о том, чтобы передать это дружественное имя дружественному параметру имени.

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте о](backup-afs.md) резервном копировании файлов Azure на портале Azure.
