---
title: Имитируйте виртуальные машины VMware в Azure с шифрованием на стороне сервера (SSE) и ключами, управляемыми клиентами (CMK) с помощью Azure Migrate Server Migration
description: Узнайте, как мигрировать VMw-мв. VM в Azure с помощью шифрования сервера (SSE) и ключей, управляемых клиентами (CMK) с помощью Azure Migrate Server Migration
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269488"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Перенос VMw VMw на Azure VMs с помощью шифрования сервера и ключей, управляемых клиентами

В этой статье описывается, как мигрировать VMw-мв с виртуальными машинами Azure с дисками, зашифрованными с помощью шифрования на стороне сервера (SSE) с помощью ключей, управляемых клиентом (CMK), с помощью Azure Migrate Server Migration (бесагентная репликация).

Интерфейс портала миграции серверов Azure Migrate Может [переносить VMs VMw в Azure с помощью репликации без агента.](tutorial-migrate-vmware.md) В настоящее время опыт работы с порталом не дает возможности включить SSE с CMK для реплицированных дисков в Azure. Возможность включить SSE с CMK для реплицированных дисков в настоящее время доступна только через REST API. В этой статье вы увидите, как создать и развернуть [шаблон менеджера ресурсов Azure,](../azure-resource-manager/templates/overview.md) чтобы воспроизвести VMware VM и настроить реплицированные диски в Azure, чтобы использовать SSE с CMK.

Примеры в этой статье используют [Azure PowerShell](/powershell/azure/new-azureps-module-az) для выполнения задач, необходимых для создания и развертывания шаблона управления ресурсами.

[Узнайте больше](../virtual-machines/windows/disk-encryption.md) о шифровании сервера (SSE) с управляемыми ключами клиента (CMK) для управляемых дисков.

## <a name="prerequisites"></a>Предварительные требования

- [Просмотрите учебник](tutorial-migrate-vmware.md) по миграции VM VMw в Azure с помощью репликации без агента, чтобы понять требования к инструментам.
- [Следуйте этим инструкциям,](how-to-add-tool-first-time.md) чтобы создать проект Azure Migrate и добавить в проект инструмент **Azure Migrate: Server Migration.**
- [Следуйте этим инструкциям,](how-to-set-up-appliance-vmware.md) чтобы настроить прибор Azure Migrate для VMware в вашей предварительной среде и полное открытие.

## <a name="prepare-for-replication"></a>Подготовка к репликации

После завершения обнаружения VM строка «Обнаруженные серверы» на плитке «Миграция сервера» покажет количество VMware VMs, обнаруженных прибором.

Прежде чем вы сможете начать репликацию ВМ, необходимо подготовить инфраструктуру репликации.

1. Создайте экземпляр sbus в целевом регионе. Сервисный автобус используется на территории прибора Azure Migrate для связи со службой миграции серверов для координации репликации и миграции.
2. Создайте учетную запись для передачи журналов операций из репликации.
3. Создайте учетную запись хранения, в которую прибор Azure Migrate загружает данные репликации.
4. Создайте Хранилище ключей и направите Хранилище ключей для управления маркерами общей подписи доступа для доступа к каплям на учетных записях хранения, созданных на шаг3 и 4.
5. Создайте токен подписи общего доступа для шины обслуживания, созданный в шаге 1, и создайте секрет для токена в Key Vault, созданный на предыдущем этапе.
6. Создайте политику доступа к Key Vault, чтобы предоставить в непредпомшную среду прибор Azure Migrate (с помощью приложения AAD) и доступ службы миграции серверов к Ключевому Убежище.
7. Создайте политику репликации и настройте службу миграции серверов с подробными сведениями об инфраструктуре репликации, созданной на предыдущем этапе.

Инфраструктура репликации должна быть создана в целевом регионе Azure для миграции и в целевой подписке Azure, в которую мигрируют mM.

Опыт портала миграции серверов упрощает подготовку инфраструктуры репликации, автоматически делая это для вас при воспроизведении VM в первый раз в проекте. В этой статье мы предположим, что вы уже реплицировали один или несколько вс-проектов с помощью интерфейса портала и что инфраструктура репликации уже создана. Мы рассмотрим, как узнать детали существующей инфраструктуры репликации и как использовать эти детали в качестве входов в шаблон Resource Manager, который будет использоваться для настройки репликации с помощью CMK.

### <a name="identifying-replication-infrastructure-components"></a>Определение компонентов инфраструктуры репликации

1. На портале Azure перейдите на страницу групп ресурсов и выберите группу ресурсов, в которой был создан проект Azure Migrate.
2. Выберите **развертывания** из левого меню и ищите имя развертывания, начиная со строки *"Microsoft.MigrateV2.VMwAreV2EnableMigrate".* Вы увидите список шаблонов Resource Manager, созданных опытом портала для настройки репликации для вс-мимо в этом проекте. Мы загрузим один такой шаблон и используем его в качестве основы для подготовки шаблона для репликации с помощью CMK.
3. Чтобы загрузить шаблон, выберите любое развертывание, соответствующее шаблону строки на предыдущем этапе > выбрать **шаблон** из левого меню > Нажмите **Скачать** из верхнего меню. Сохранить файл template.json локально. Вы будете отсеивать этот шаблон файл в последний шаг.

## <a name="create-a-disk-encryption-set"></a>Создание набора шифрования дисков

Шифрование диска устанавливает объектные карты Управляемые диски в Key Vault, который содержит CMK для использования для SSE. Чтобы воспроизвести ВМ с помощью CMK, вы создадите набор шифрования дисков и передайте его в качестве ввода в операцию репликации.

Следуйте примеру [здесь,](../virtual-machines/windows/disk-encryption.md#powershell) чтобы создать набор шифрования дисков с помощью Azure PowerShell. Убедитесь, что набор шифрования дисков создан в целевой подписке, в которую мигрируют в и в целевом регионе Azure для миграции.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Получить подробную информацию о VMware VM мигрировать

На этом этапе вы будете использовать Azure PowerShell, чтобы получить подробную информацию о VM, который необходимо перенести. Эти данные будут использованы для построения шаблона управления ресурсами для репликации. В частности, два свойства, представляющие интерес являются:

- Идентификатор ресурса для обнаруженных VMs.
- Список дисков для VM и их идентификаторов дисков.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Копируйте значение строки SiteId, соответствующее прибору Azure Migrate, через который обнаружен VM. В приведенном выше примере SiteId - *"/подписка/509099b2-9d2c-4636-b43e-bd5cafb69/resourceGroups/ContosoVMwCMK/providers/Microsoft.OffAzure/VMwareSites/VMwarelianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Копируйте значения ResourceId, имя и диск uuid для машины, которая будет перенесена.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Создание шаблона диспетчера ресурсов для репликации

- Откройте файл шаблона resource Manager, который вы загрузили в **компонентах инфраструктуры идентификации** в редакторе по вашему выбору.
- Удалите все определения ресурсов из шаблона, за исключением ресурсов типа *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Если существует несколько определений ресурсов вышеупомянутого типа, удалите все, кроме одного. Удалите все определения свойств **из** определения ресурса.
- В конце этого шага, вы должны иметь файл, который выглядит как пример ниже и имеет тот же набор свойств.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Изнайте свойство **имени** в определении ресурса. Замените строку, которая следует за последним "/" в свойстве имени, со значением *$machine. Имя*(с предыдущего шага).
- Измените стоимость **свойства.providerSpecificDetails.vmwareMachineId** собственности со стоимостью *$machine. ResourceId*(с предыдущего шага).
- Установите значения для **targetResourceGroupId,** **targetNetworkId,** **targetSubnetName** для идентификатора целевой группы ресурсов, целевого идентификатора виртуальных сетевых ресурсов и имени целевого поднета соответственно.
- Установите значение **лицензииType** на "WindowsServer", чтобы применить Azure Hybrid Benefit для этого VM. Если этот VM не имеет права на получение гибридной выгоды Azure Hybrid Benefit, установите значение **лицензииType** на NoLicenseType.
- Измените значение свойства **targetVmName** на желаемое имя виртуальной машины Azure для мигрированного VM.
- Дополнительно добавляйте свойство, названное **targetVmSize** ниже свойства **targetVmName.** Установите значение свойства **targetVmSize** к желаемому размеру виртуальной машины Azure для мигрировавшей VM.
- Свойство **disksToInclude** представляет собой список входных данных диска для репликации с каждым элементом списка, представляющим один припредстательный диск. Создайте столько элементов списка, сколько количество дисков на предприимчивом VM. Замените свойство **diskId** в элементе списка на uuid дисков, определенных на предыдущем этапе. Установите значение **isOSDisk** на "истинное" для Диска ОС VM и "ложное" для всех других дисков. Оставьте свойства **logStorageAccountId** и **logStorageAccountSasSecretName** без изменений. Установите значение **diskType** в тип Azure Managed Disk *(Standard_LRS, Premium_LRS, StandardSSD_LRS)* для использования для диска. Для дисков, которые должны быть зашифрованы с ПОМОЩЬю CMK, добавьте свойство под названием **diskEncryptionSetId** и установите значение ресурса ID созданного набора шифрования диска **($des. Id**) в *настройке набора шифрования дисков*
- Сохранить отредактированный файл шаблона. В приведенном выше примере отредактированный файл шаблона выглядит следующим образом:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Настройка репликации

Теперь можно развернуть отредактированный шаблон Ресурсного менеджера в группу ресурсов проекта для настройки репликации для VM. Узнайте, как [развернуть ресурс с шаблонами управления ресурсами Azure и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Дальнейшие действия

Мониторинг состояния [репликации](tutorial-migrate-vmware.md#track-and-monitor) через опыт портала и выполнение тестовых миграций и миграции.
