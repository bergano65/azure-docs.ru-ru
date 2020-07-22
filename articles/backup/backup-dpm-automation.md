---
title: Использование PowerShell для архивации рабочих нагрузок DPM
description: Узнайте о том, как развернуть службу архивации Azure для Data Protection Manager (DPM) и управлять ей с помощью PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 4d8b8f6ca233c997bc2a94f88903d14009481d37
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538859"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Развертывание резервного копирования в Azure для серверов Data Protection Manager (DPM) и управление им с помощью PowerShell

В этой статье описано, как использовать PowerShell для настройки службы архивации Azure на сервере DPM и для управления резервным копированием и восстановлением данных.

## <a name="setting-up-the-powershell-environment"></a>Настройка среды PowerShell

Прежде чем использовать PowerShell для управления резервными копиями Data Protection Manager в Azure, необходимо настроить подходящую среду в PowerShell. В начале сеанса PowerShell обязательно выполните следующую команду для импорта необходимых модулей, которая также позволяет правильно ссылаться на командлеты DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Настройка и регистрация

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы начать, [загрузите последнюю версию Azure PowerShell](/powershell/azure/install-az-ps).

С помощью PowerShell можно автоматизировать указанные ниже задачи по настройке и регистрации.

* Создание хранилища Служб восстановления
* Установка агента службы архивации Azure.
* Регистрация в службе архивации Azure
* Параметры сети
* Параметры шифрования.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Чтобы создать хранилище служб восстановления, выполните описанные ниже действия. Хранилище служб восстановления отличается от хранилища службы архивации.

1. Если вы используете службу архивации Azure впервые, выполните командлет **Register-AzResourceProvider**, чтобы зарегистрировать поставщик служб восстановления Azure в своей подписке.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Хранилище служб восстановления представляет собой ресурс ARM, поэтому вам потребуется разместить его в группе ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую. При создании группы ресурсов укажите ее имя и расположение.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Выполните командлет **New-AzRecoveryServicesVault**, чтобы создать хранилище. Разместите хранилище там же, где находится группа ресурсов.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Укажите тип избыточности хранилища, который будет использоваться. можно использовать [локально избыточное хранилище (LRS)](../storage/common/storage-redundancy.md) или [геоизбыточное хранилище (GRS)](../storage/common/storage-redundancy.md). В следующем примере показано, что для параметра BackupStorageRedundancy для testVault задано значение GeoRedundant.

   > [!TIP]
   > Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных. По этой причине объект хранилища служб восстановления резервных копий удобно хранить в переменной.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке

Чтобы получить список всех хранилищ в текущей подписке, используйте командлет **Get- AzRecoveryServicesVault**. Он позволяет убедиться в том, что хранилище создано, и увидеть, какие хранилища доступны в подписке.

Выполнив команду Get-AzRecoveryServicesVault, вы получите список всех хранилищ в подписке.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Установка агента службы архивации Azure на сервер DPM

Прежде чем устанавливать агент службы архивации Azure, необходимо загрузить установщик и разместить его в системе Windows Server. Последнюю версию установщика можно загрузить в [центре загрузки Майкрософт](https://aka.ms/azurebackup_agent) или на странице панели мониторинга для хранилища служб восстановления. Сохраните установщик в удобном для вас месте, например в папке `C:\Downloads\*`.

Чтобы установить агент, в консоли PowerShell с повышенными привилегиями **на сервере DPM**выполните следующую команду:

```powershell
MARSAgentInstaller.exe /q
```

Агент будет установлен с параметрами по умолчанию. Установка займет всего несколько минут и пройдет в фоновом режиме. Если параметр */nu* не будет указан, в конце установки откроется окно **Обновления Windows** для проверки наличия обновлений.

Агент появится в списке установленных программ. Чтобы просмотреть список установленных программ, выберите **Панель управления** > **Программы** > **Программы и компоненты**.

![Агент установлен](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Варианты установки

Чтобы просмотреть все доступные в командной строке параметры, используйте следующую команду:

```powershell
MARSAgentInstaller.exe /?
```

Доступны следующие параметры.

| Параметр | Сведения | По умолчанию |
| --- | --- | --- |
| /q |Позволяет выполнить тихую установку. |- |
| /p:"расположение" |Путь к папке установки для агента архивации Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"расположение" |Путь к папке кэша для агента архивации Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Позволяет явно согласиться на использование Центра обновления Майкрософт. |- |
| /nu |Позволяет отказаться от проверки наличия обновлений после завершения установки. |- |
| /d |Удаляет агент служб восстановления Microsoft Azure. |- |
| /ph |Адрес узла прокси-сервера. |- |
| /po |Номер порта узла прокси-сервера. |- |
| /pu |Имя пользователя узла прокси-сервера. |- |
| /pw |Пароль прокси-сервера. |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Регистрация DPM в хранилище служб восстановления

После создания хранилища служб восстановления скачайте последнюю версию агента и учетные данные хранилища и сохраните их в удобном расположении, например C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

На сервере DPM запустите командлет [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) , чтобы зарегистрировать компьютер в хранилище.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Исходные параметры конфигурации

Зарегистрированный в хранилище служб восстановления сервер DPM запускается с параметрами подписки, используемыми по умолчанию. Эти параметры подписки включают настройки сети, шифрования и промежуточной области. Чтобы изменить параметры подписки, сначала получите дескриптор существующих параметров (по умолчанию) с помощью командлета [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Все изменения вносятся в этот локальный объект PowerShell ```$setting```, а затем полный объект фиксируется в DPM и службе архивации Azure с помощью командлета [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019). Таким образом он сохраняется. Чтобы гарантировать, что изменения будут сохранены, необходимо использовать флаг ```–Commit```. Параметры не будут применяться и использоваться службой архивации Azure до тех пор, пока они не будут зафиксированы.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Сеть

Если компьютер DPM подключен к службе архивации Azure в Интернете через прокси-сервер, для успешного резервного копирования следует указать параметры прокси-сервера. Для этого используются параметры ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` и ```ProxyPassword``` для командлета [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019). В нашем случае прокси-сервер не используется, поэтому мы явным образом удаляем все данные прокси-сервера.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Управлять использованием пропускной способности для выбранных дней недели можно с помощью параметров ```-WorkHourBandwidth``` и ```-NonWorkHourBandwidth``` В этом примере мы не настраиваем регулирование.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Настройка промежуточной области

Для агента службы архивации Azure, работающего на сервере DPM, необходимо предоставить временное хранилище для восстановленных из облака данных (локальная промежуточная область). Настройте промежуточную область с помощью командлета [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) с параметром ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

В примере выше задается промежуточная область *C:\StagingArea* в объекте PowerShell ```$setting```. Убедитесь, что указанная папка уже существует, иначе финальная фиксация параметров подписки завершится ошибкой.

### <a name="encryption-settings"></a>Параметры шифрования.

Для защиты конфиденциальности данных резервные копии данных, отправляемые в службу архивации Azure, зашифровываются. Используемая для шифрования парольная фраза является "паролем" для расшифровки данных во время их восстановления. После создания фразы надежно сохраните ее и никому не сообщайте о ней.

В следующем примере первая команда преобразует строку ```passphrase123456789``` в защищенную строку и присваивает ее переменной ```$Passphrase```. Вторая команда задает защищенную строку в ```$Passphrase``` в качестве пароля для шифрования резервных копий.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> После создания парольной фразы надежно сохраните ее и никому не сообщайте о ней. Восстановить данные из Azure без парольной фразы невозможно.
>
>

На этом этапе следует внести все необходимые изменения в объект ```$setting``` . Не забудьте зафиксировать изменения.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Защита данных в службе архивации Azure

В этом разделе мы добавим рабочий сервер в DPM, а затем включим защиту данных в локальном хранилище DPM и службе архивации Azure. На примерах мы покажем, как создавать резервные копии файлов и папок. Таким же способом можно создавать резервные копии любых поддерживаемых DPM источников данных. Все резервные копии DPM находятся под управлением группы защиты (ГЗ), которая состоит из четырех частей.

1. **Члены группы защиты** — это список всех защищаемых объектов (также называемых *источниками данных* в DPM), которые вы хотите защитить в рамках одной группы защиты. Например, вы можете защитить рабочие виртуальные машины в одной группе защиты, а базы данных SQL Server — в другой группе защиты, так как у них могут быть разные требования к резервному копированию. Перед созданием резервных копий данных на рабочем сервере убедитесь, что на рабочем сервере установлен агент DPM, который управляется с помощью DPM. Выполните шаги по [установке агента DPM](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) и связыванию его с соответствующим сервером DPM.
2. **Метод защиты данных** — определяет расположения резервных копий (магнитная лента, диск и облако). В нашем примере мы организуем защиту данных на локальный диск и в облако.
3. **Расписание резервного копирования** — указывает, когда необходимо выполнять резервное копирование и как часто следует синхронизировать данные между сервером DPM и рабочим сервером.
4. **Расписание хранения** — определяет период хранения точек восстановления в Azure.

### <a name="creating-a-protection-group"></a>Создание группы защиты

Начните с создания новой группы защиты с помощью командлета [New-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Указанный выше командлет создает группу защиты с именем *ProtectGroup01*. Существующую группу защиты можно изменить позднее, чтобы добавить резервную копию в облако Azure. Тем не менее, чтобы получить возможность вносить изменения в группу защиты (новую или существующую), необходимо получить дескриптор *изменяемого* объекта с помощью командлета [Get-DPMModifiableProtectionGroup](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Добавление членов группы в группу защиты

Каждому агенту DPM известен список источников данных на сервере, на котором он установлен. Чтобы добавить источник данных в группу защиты, агент DPM должен сначала отправить список источников данных обратно на сервер DPM. Затем выбирается один или несколько источников данных, которые добавляются в группу защиты. Ниже представлены действия, которые необходимо выполнить в PowerShell.

1. Получите список всех серверов, управляемых DPM через агент DPM.
2. Выберите определенный сервер.
3. Получите список всех источников данных на сервере.
4. Выберите один или несколько источников данных и добавьте их в группу защиты.

Чтобы получить список серверов, на которых установлен агент DPM и которые управляются сервером DPM, воспользуйтесь командлетом [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) . В этом примере мы отфильтруем и настроим для резервного копирования только сервер с именем *productionserver01* .

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Теперь получим список источников данных на ```$server``` с помощью командлета [Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019). В этом примере мы применяем фильтрацию для тома `D:\`, который нужно настроить для резервного копирования. Затем этот источник данных добавляется в группу защиты с помощью командлета [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019). Не забывайте использовать *изменяемый* объект группы защиты ```$MPG```, чтобы вносить дополнения.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

При необходимости повторите этот шаг несколько раз, пока все выбранные источники данных не будут добавлены в группу защиты. Для начала можно выполнить рабочий процесс создания группы защиты только для одного источника данных, а затем позже добавить в эту группу дополнительные источники данных.

### <a name="selecting-the-data-protection-method"></a>Выбор способа защиты данных

После добавления в группу защиты источников данных необходимо указать метод защиты, воспользовавшись для этого командлетом [Set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) . В этом примере группа защиты настроена для резервного копирования на локальный диск и в облако. Необходимо также с помощью командлета [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) с флагом -Online указать источник данных, который нужно защитить в облаке.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Настройка диапазона хранения

Задайте период хранения для точек резервного копирования с помощью командлета [Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) . Несмотря на то что задание периода хранения до определения расписания резервного копирования может показаться странным, командлет ```Set-DPMPolicyObjective``` автоматически задает расписание резервного копирования по умолчанию, которое в последствии можно изменить. Вы всегда можете сначала определить расписание резервного копирования, а затем задать политику хранения.

В примере ниже командлет задает параметры хранения для резервного копирования на диск. Резервные копии будут храниться 10 дней, а данные будут синхронизироваться между рабочим сервером и сервером DPM каждые 6 часов. ```SynchronizationFrequencyMinutes``` определяет не частоту создания точек резервного копирования, а частоту копирования данных на сервер DPM.  Это предотвращает создание слишком крупных резервных копий.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Для резервных копий, сохраняемых в Azure (в DPM это называется Online Backup), диапазоны хранения можно настроить для [долгосрочного хранения с использованием трехуровневой схемы (GFS)](backup-azure-backup-cloud-as-tape.md). То есть можно определить политику объединенного хранения, включающую политики ежедневного, еженедельного, ежемесячного и ежегодного хранения. В этом примере мы создаем массив, представляющий сложную схему хранения, которая нам необходима, а затем настраиваем диапазон хранения с помощью командлета [Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Настройка расписания резервного копирования

При указании цели защиты с помощью командлета ```Set-DPMPolicyObjective``` DPM автоматически задаст расписание резервного копирования по умолчанию. Для изменения расписания по умолчанию используйте командлет [Get DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019), а затем командлет [Set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019).

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

В приведенном выше примере ```$onlineSch``` — это массив с четырьмя элементами, содержащий существующее расписание оперативной защиты для группы защиты в схеме GFS:

1. ```$onlineSch[0]``` содержит ежедневное расписание.
2. ```$onlineSch[1]``` содержит еженедельное расписание.
3. ```$onlineSch[2]``` содержит ежемесячное расписание.
4. ```$onlineSch[3]``` содержит ежегодное расписание.

Поэтому если необходимо изменить еженедельное расписание, необходимо ссылаться на ```$onlineSch[1]```.

### <a name="initial-backup"></a>Начальное резервное копирование

Если вы создаете резервные копии источника данных впервые, DPM создаст исходную реплику, которая, в свою очередь, создаст в томе реплики DPM полную копию защищаемого источника данных. Это действие можно запланировать на определенное время или выполнить вручную с помощью командлета [Set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) с параметром ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Изменение размера реплики DPM и тома точек восстановления

Вы можете также изменить размер тома реплики DPM и тома теневых копий с помощью командлета [Set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019), как показано в следующем примере: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Фиксация изменений в группу защиты

Наконец, необходимо зафиксировать изменения до того, как DPM выполнит резервное копирование в соответствии с новой конфигурацией группы защиты. Для этого воспользуйтесь командлетом [Set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Просмотр точек резервного копирования

Чтобы получить список всех точек восстановления для источника данных, можно использовать командлет [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) . В этом примере мы:

* выгрузим данные из всех групп защиты на сервер DPM, которые будут храниться в массиве ```$PG```
* получим источники данных, соответствующие ```$PG[0]```
* получим все точки восстановления для источника данных.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Восстановление данных, защищенных в Azure

Восстановление данных представляет собой сочетание объектов ```RecoverableItem``` и ```RecoveryOption```. В предыдущем разделе мы получили список точек резервного копирования для источника данных.

В примере ниже демонстрируется восстановление виртуальной машины Hyper-V из службы архивации Azure путем объединения резервных точек с целевым объектом для восстановления. В этом примере мы:

* Создадим параметр восстановления, используя командлет [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019).
* Выполним выборку массива точек резервного копирования с помощью командлета ```Get-DPMRecoveryPoint``` .
* Выберем точку резервного копирования для восстановления.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Команды можно с легкостью расширить для любого типа источника данных.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о службе архивации Azure для DPM см. в статье [Подготовка к архивированию рабочих нагрузок в Azure с помощью DPM](backup-azure-dpm-introduction.md).
