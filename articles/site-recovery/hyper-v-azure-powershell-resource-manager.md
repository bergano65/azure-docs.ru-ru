---
title: Гипер-V VM аварийного восстановления с помощью Azure восстановления сайта и PowerShell
description: Автоматизируйте аварийное восстановление виртуальных машин Hyper-V в Azure с помощью службы Azure Site Recovery, используя PowerShell и Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257996"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager

Служба [Azure Site Recovery](site-recovery-overview.md) помогает реализовать стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR), выполняя репликацию, отработку отказа и восстановление виртуальных машин Azure, а также локальных виртуальных машин и физических серверов.

Здесь описывается как использовать Windows PowerShell вместе с Azure Resource Manager для репликации виртуальных машин Hyper-V в Azure. В примере, используемом в этой статье, показано, как выполнить репликацию одной виртуальной машины на узле Hyper-V в Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell предоставляет командлеты для управления Azure, используя Windows PowerShell. Командлеты PowerShell для службы Site Recovery, доступные в Azure PowerShell для диспетчера ресурсов Azure, позволяют обеспечить защиту и выполнить восстановление серверов в Azure.

Для работы с этой статьей не требуется большой опыт работы с PowerShell, но необходимо знакомство с основными понятиями, такими как модули, командлеты и сеансы. Для получения дополнительной информации см. [документацию PowerShell](/powershell) и [использование Azure PowerShell с менеджером ресурсов Azure.](../powershell-azure-resource-manager.md)

> [!NOTE]
> Партнеры корпорации Майкрософт в программе для поставщиков облачных решений могут настроить защиту серверов клиентов и управлять ею в своих подписках CSP (клиентские подписки).

## <a name="before-you-start"></a>Перед началом работы

Убедитесь, что выполнены следующие предварительные требования.

- Учетная запись [Microsoft Azure](https://azure.microsoft.com/) . Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). Также вы можете ознакомиться с [расценками на использование менеджера Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Установите Azure PowerShell. Для получения информации об этом выпуске и о том, как его установить, можно узнать об [установке Azure PowerShell.](/powershell/azure/install-az-ps)

К тому же конкретный пример, описанный в этой статье, требует следующих компонентов:

- Узел Hyper-V под управлением Windows Server 2012 R2 или Microsoft Hyper-V Server 2012 R2, содержащий одну или несколько виртуальных машин. Серверы Hyper-V должны быть подключены к Интернету напрямую или через прокси-сервер.
- Виртуальные машины, которые вы хотите реплицировать, должны соответствовать [этим условиям](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Шаг 1. Вход в учетную запись Azure

1. Откройте консоль PowerShell и выполните следующую команду, чтобы войти в учетную запись Azure. Cmdlet поднимает веб-страницу подсказывает вам для `Connect-AzAccount`ваших учетных данных счета: .
   - Кроме того, вы можете включить учетные данные учетной записи в качестве параметра в `Connect-AzAccount` cmdlet, используя параметр **credential.**
   - Если вы являетесь партнером CSP, работающим от имени арендатора, укажите клиента в качестве арендатора, используя его основное доменное имя арендатора или арендатора. Например: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Свяжите подписку, которую собираетесь использовать, с учетной записью, так как последняя может иметь несколько подписок:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Убедитесь, что в вашей подписке зарегистрированы поставщики Azure для служб восстановления и службы Site Recovery, используя команды, приведенные ниже:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Убедившись, что в выходных данных команд для параметра **RegistrationState** установлено значение **Registered**, можно перейти к шагу 2. В противном случае необходимо зарегистрировать отсутствующего поставщика в своей подписке, выполнив следующие команды:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Убедитесь, что поставщики успешно зарегистрированы, выполнив следующие команды:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Шаг 2. Настройка хранилища

1. Создайте группу ресурсов для Azure Resource Manager, которой необходимо создать хранилища, или воспользуйтесь существующими группами ресурсов. Создайте группу ресурсов следующим образом. Переменная `$ResourceGroupName` содержит имя группы ресурсов, которую вы хотите создать, а $Geo переменная содержит область Azure, в которой для создания группы ресурсов (например, "Бразилия на юге").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Чтобы получить список групп ресурсов в `Get-AzResourceGroup` подписке, запустите cmdlet.
1. Создайте хранилище служб восстановления Azure следующим образом:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Вы можете получить список существующих хранилищ с `Get-AzRecoveryServicesVault` cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Шаг 3. Настройка контекста для хранилища служб восстановления

Задайте контекст хранилища следующим образом:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Шаг 4. Создание узла Hyper-V

1. Создайте сайт Hyper-V следующим образом:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Этот командлет запускает задание Site Recovery для создания сайта и возвращает объект этого задания. Дождитесь завершения задания и убедитесь, что оно выполнено успешно.
1. Используйте `Get-AzRecoveryServicesAsrJob` cmdlet для получения объекта задания и проверьте текущее состояние задания.
1. Создайте и скачайте ключ регистрации для сайта, выполнив следующие действия:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Скопируйте скачанный ключ на узел Hyper-V. Этот ключ вам потребуется, чтобы зарегистрировать узел Hyper-V на сайте.

## <a name="step-5-install-the-provider-and-agent"></a>Шаг 5. Установка поставщика и агента

1. Скачать установщик для последней версии поставщика от [Microsoft](https://aka.ms/downloaddra).
1. Запустите установщик на хосте Hyper-V.
1. По завершении установки перейдите к шагу регистрации.
1. При появлении запроса укажите скачанный ключ и завершите регистрацию узла Hyper-V.
1. Убедитесь, что узел Hyper-V зарегистрирован на сайте следующим образом:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Если вы используете основной сервер Hyper-V, скачайте файл установки и сделайте следующее:

1. Извлеките файлы из _AzureSiteRecoveryRecoveryProvider.exe_ в локальный каталог, запустив эту команду:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Выполните следующую команду:

   ```console
   .\setupdr.exe /i
   ```

   Результаты регистрируются на _:ProgramData%-ASRLogs-DRASetupWizard.log_.

1. Зарегистрируйте сервер с помощью следующей команды:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Шаг 6. Создание политики репликации

Перед запуском указанная учетная запись хранилища должна находиться в той же области Azure, что и хранилище, и должна быть включена георепликация.

1. Создайте политику репликации, выполнив следующую команду:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Проверьте возвращенный результат задания, чтобы убедиться, что политика репликации успешно создана.

1. Получите соответствующий сайту контейнер защиты следующим образом:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Свяжите контейнер защиты с политикой репликации, выполнив следующую команду:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Дождитесь завершения этого задания и убедитесь, что оно выполнено успешно.

1. Извлеките отображение защитного контейнера.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Шаг 7. Включение защиты в виртуальных машинах

1. Получите защищаемый элемент, соответствующий виртуальной машине, которую необходимо защитить, выполнив следующую команду:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Включите защиту виртуальной машины. Если VM, который вы защищаете, имеет более одного диска, прикрепленного к нему, укажите диск операционной системы с помощью параметра **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Дождитесь, пока виртуальные машины перейдут в защищенное состояние после начальной репликации. Это может занять некоторое время в зависимости от таких факторов, как объем данных для репликации и доступная пропускная способность вышестоящих служб в Azure. Когда виртуальные машины перейдут в защищенное состояние, параметры State и StateDescription задания обновятся следующим образом:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Обновляйте свойства восстановления (например, размер роли VM) и сеть Azure, к которой можно прикрепить VM NIC после сбоя.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Если вы хотите воспроизвести управляемые диски CMK в Azure, сделайте следующие шаги с помощью Az PowerShell 3.3.0 далее:
>
> 1. Включить отказ в управляемых дисках путем обновления свойств VM
> 1. Используйте `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet для получения идентификатора диска для каждого диска защищенного элемента
> 1. Создайте объект словаря, используя `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet, чтобы содержать отображение идентификатора диска в набор шифрования диска. Эти наборы шифрования дисков должны быть предварительно созданы вами в целевом регионе.
> 1. Обновление свойств VM `Set-AzRecoveryServicesAsrReplicationProtectedItem` с помощью cmdlet путем передачи словарного объекта в параметре **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Шаг 8. Запуск тестовой отработки отказа

1. Запустите тестовую отработку отказа, выполнив следующую команду:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Убедитесь, что тестовая виртуальная машина создана в Azure. После создания тестовой виртуальной машины в Azure, задание тестовой отработки отказа будет приостановлено.
1. Для очистки и завершения тестовой отработки отказа выполните следующую команду:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте больше](/powershell/module/az.recoveryservices) об использовании командлетов PowerShell инструмента Azure Resource Manager для службы Azure Site Recovery.
