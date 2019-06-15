---
title: Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager | Документация Майкрософт
description: Автоматизируйте аварийное восстановление виртуальных машин Hyper-V в Azure с помощью службы Azure Site Recovery, используя PowerShell и Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5fbe4fd5f85026cd62f1bd10e36561b312464054
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690569"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager

Служба [Azure Site Recovery](site-recovery-overview.md) помогает реализовать стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR), выполняя репликацию, отработку отказа и восстановление виртуальных машин Azure, а также локальных виртуальных машин и физических серверов.

Здесь описывается как использовать Windows PowerShell вместе с Azure Resource Manager для репликации виртуальных машин Hyper-V в Azure. В примере, используемом в этой статье, показано, как выполнить репликацию одной виртуальной машины на узле Hyper-V в Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell предоставляет командлеты для управления Azure, используя Windows PowerShell. Командлеты PowerShell для службы Site Recovery, доступные в Azure PowerShell для диспетчера ресурсов Azure, позволяют обеспечить защиту и выполнить восстановление серверов в Azure.

Для работы с этой статьей не требуется большой опыт работы с PowerShell, но необходимо знакомство с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения см. в статьях [Начало работы с Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx) и [Управление ресурсами с помощью Azure PowerShell и Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Партнеры корпорации Майкрософт в программе для поставщиков облачных решений могут настроить защиту серверов клиентов и управлять ею в своих подписках CSP (клиентские подписки).
>
>

## <a name="before-you-start"></a>Перед началом работы
Убедитесь, что выполнены следующие предварительные требования.

* Учетная запись [Microsoft Azure](https://azure.microsoft.com/) . Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). Также вы можете ознакомиться с [расценками на использование менеджера Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
* Установите Azure PowerShell. Сведения об этом выпуске и его установке см. в разделе [установить Azure PowerShell](/powershell/azure/install-az-ps).

К тому же конкретный пример, описанный в этой статье, требует следующих компонентов:

* Узел Hyper-V под управлением Windows Server 2012 R2 или Microsoft Hyper-V Server 2012 R2, содержащий одну или несколько виртуальных машин. Серверы Hyper-V должны быть подключены к Интернету напрямую или через прокси-сервер.
* Виртуальные машины, которые вы хотите реплицировать, должны соответствовать [этим условиям](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Шаг 1. Вход в учетную запись Azure

1. Откройте консоль PowerShell и выполните следующую команду, чтобы войти в учетную запись Azure. Этот командлет открывает веб-станицу, на которой пользователю предлагается ввести данные для входа в учетную запись — **Подключение AzAccount**.
    - Кроме того, учетные данные можно добавить в качестве параметра в командлет **Connect-AzAccount**, используя параметр **-Credential**.
    - Если вы — партнер-поставщик облачных услуг, работающий от имени клиента, вам потребуется указать заказчика в качестве клиента. Для этого нужно ввести идентификатор или основное доменное имя клиента. Пример: **Connect-AzAccount-Tenant "fabrikam.com"**
2. Свяжите подписку, которую собираетесь использовать, с учетной записью, так как последняя может иметь несколько подписок:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Убедитесь, что в вашей подписке зарегистрированы поставщики Azure для служб восстановления и службы Site Recovery, используя команды, приведенные ниже:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Убедившись, что в выходных данных команд для параметра **RegistrationState** установлено значение **Registered**, можно перейти к шагу 2. В противном случае необходимо зарегистрировать отсутствующего поставщика в своей подписке, выполнив следующие команды:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Убедитесь, что поставщики успешно зарегистрированы, выполнив следующие команды:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>Шаг 2. Настройка хранилища

1. Создайте группу ресурсов для Azure Resource Manager, которой необходимо создать хранилища, или воспользуйтесь существующими группами ресурсов. Создайте группу ресурсов следующим образом. Переменная $ResourceGroupName содержит имя создаваемой группы ресурсов, а переменная $Geo — регион Azure, в котором ее необходимо создать (например, Южная часть Бразилии).

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Для получения списка групп ресурсов в вашей подписке запустите **Get AzResourceGroup** командлета.
2. Создайте хранилище служб восстановления Azure следующим образом:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Можно получить список существующих хранилищ с **Get AzRecoveryServicesVault** командлета.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Шаг 3. Настройка контекста для хранилища Служб восстановления

Задайте контекст хранилища следующим образом:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Шаг 4. Создание сайта Hyper-V

1. Создайте сайт Hyper-V следующим образом:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Этот командлет запускает задание Site Recovery для создания сайта и возвращает объект этого задания. Дождитесь завершения задания и убедитесь, что оно выполнено успешно.
3. Для получения объекта задания и проверки его текущего состояния используйте командлет **Get-AsrJob**.
4. Создайте и скачайте ключ регистрации для сайта, выполнив следующие действия:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Скопируйте скачанный ключ на узел Hyper-V. Этот ключ вам потребуется, чтобы зарегистрировать узел Hyper-V на сайте.

## <a name="step-5-install-the-provider-and-agent"></a>Шаг 5. Установка поставщика и агента

1. Скачайте установщик последней версии поставщика с веб-сайта [корпорации Майкрософт](https://aka.ms/downloaddra).
2. Запустите установщик на узле Hyper-V.
3. По завершении установки перейдите к шагу регистрации.
4. При появлении запроса укажите скачанный ключ и завершите регистрацию узла Hyper-V.
5. Убедитесь, что узел Hyper-V зарегистрирован на сайте следующим образом:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Шаг 6. Создание политики репликации

Перед началом убедитесь, что указанная учетная запись хранения находится в том же регионе Azure, что и хранилище. В ней также должна быть включена функция георепликации.

1. Создайте политику репликации, выполнив следующую команду:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Проверьте возвращенный результат задания, чтобы убедиться, что политика репликации успешно создана.

3. Получите соответствующий сайту контейнер защиты следующим образом:

        $protectionContainer = Get-AsrProtectionContainer
3. Свяжите контейнер защиты с политикой репликации, выполнив следующую команду:

     $Policy = Get-AsrPolicy -FriendlyName $PolicyName   $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]

4. Дождитесь завершения этого задания и убедитесь, что оно выполнено успешно.

## <a name="step-7-enable-vm-protection"></a>Шаг 7. Включение защиты в виртуальных машинах

1. Получите защищаемый элемент, соответствующий виртуальной машине, которую необходимо защитить, выполнив следующую команду:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Включите защиту виртуальной машины. Если к виртуальной машине, которую необходимо защитить, подключено несколько дисков, нужно указать диск операционной системы (ОС) с помощью параметра *OSDiskName* .

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS Windows -RecoveryResourceGroupId

3. Дождитесь, пока виртуальные машины перейдут в защищенное состояние после начальной репликации. Это может занять некоторое время в зависимости от таких факторов, как объем данных для репликации и доступная пропускная способность вышестоящих служб в Azure. Когда виртуальные машины перейдут в защищенное состояние, параметры State и StateDescription задания обновятся следующим образом:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Обновите свойства восстановления (например, размер роли виртуальной машины ) и сеть Azure, к которой подключится сетевой адаптер виртуальной машины после отработки отказа.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>Шаг 8. Запуск тестовой отработки отказа
1. Запустите тестовую отработку отказа, выполнив следующую команду:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Убедитесь, что тестовая виртуальная машина создана в Azure. После создания тестовой виртуальной машины в Azure, задание тестовой отработки отказа будет приостановлено.
3. Для очистки и завершения тестовой отработки отказа выполните следующую команду:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Дальнейшие действия
[Узнайте больше](https://docs.microsoft.com/powershell/module/az.recoveryservices) об использовании командлетов PowerShell инструмента Azure Resource Manager для службы Azure Site Recovery.
