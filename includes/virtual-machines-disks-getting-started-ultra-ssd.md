---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008653"
---
Ультрадиски Azure предлагают высокую пропускную емкость, высокую емкость IOPS и последовательную систему хранения дисков с низкой задержкой для виртуальных машин Azure IaaS (VM). Это новое предложение обеспечивает первоклассную производительность с тем же уровнем доступности, что и имеющиеся предложения дисков. Одним из основных преимуществ ультра дисков является возможность динамического изменения производительности SSD вместе с рабочими нагрузками без необходимости перезагрузки вашего VMs. Диски категории "Ультра" подходят для рабочих нагрузок, предполагающих интенсивную работу с данными, например SAP HANA, базы данных верхнего уровня и рабочие нагрузки с большим количеством транзакций.

## <a name="ga-scope-and-limitations"></a>Сфера охвата и ограничения ГА

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Определение размера ИТ и наличия региона

### <a name="vms-using-availability-zones"></a>ВМ, использующие зоны доступности

Чтобы использовать ультра диски, необходимо определить, в какой зоне доступности вы находитесь. Не каждый регион поддерживает каждый размер VM с ультра дисками. Чтобы определить, поддерживает ли ваш регион, зону и размер VM ультрадиски, запустите одну из следующих команд, убедитесь, что сначала замените **область,** **vmSize**и значения **подписки:**

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Ответ будет аналогичен форме ниже, где X является зоной для развертывания в выбранном вами регионе. Значением X может иметь 1, 2 или 3.

Сохранить значение **зон,** он представляет вашу зону доступности, и вы будете нуждаться в нем для того, чтобы развернуть диск Ultra.

|ResourceType  |name  |Расположение  |Зоны  |Ограничение  |Функция  |Значение  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Если ответа от команды не последовало, то выбранный размер VM не поддерживается ультрадисками в выбранной области.

Теперь, когда вы знаете, к какой зоне развертывать, выполните этапы развертывания в этой статье, чтобы либо развернуть VM с прикрепленным ультра диском, либо прикрепить ультрадиск к существующему VM.

### <a name="vms-with-no-redundancy-options"></a>VMs без вариантов избыточности

Ультра диски, развернутые в Западной ЧАСТИ США, должны быть развернуты без каких-либо вариантов избыточности, на данный момент. Однако, не каждый размер диска, который поддерживает ультра диски могут быть в этой области. Чтобы определить, какие из них в Западной США поддержки ультра дисков, вы можете использовать любой из следующих фрагментов кода. Убедитесь в `vmSize` том, чтобы заменить и `subscription` значения в первую очередь:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Ответ будет похож на следующую `UltraSSDAvailable   True` форму, указывает ли размер VM поддерживает ультра диски в этом регионе.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Развертывание ультра-диска с помощью менеджера ресурсов Azure

Во-первых, определите размер VM для развертывания. Список поддерживаемых размеров VM можно узнать в разделе [область охвата и ограничений GA.](#ga-scope-and-limitations)

Если вы хотите создать VM с несколькими ультра дисками, обратитесь к образцу [Создать VM с несколькими ультра дисков.](https://aka.ms/ultradiskArmTemplate)

Если вы собираетесь использовать свой собственный шаблон, `Microsoft.Compute/Disks` убедитесь, `2018-06-01` что **apiVersion** для `Microsoft.Compute/virtualMachines` и устанавливается как (или позже).

Установите диск sku **UltraSSD_LRS,** затем установите емкость диска, IOPS, зону доступности и пропускную способность в MBps для создания ультра диска.

После подготовки виртуальной машины можно будет секционировать и отформатировать ее диски данных, а затем настроить их для рабочих нагрузок.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Развертывание ультрадиска с помощью портала Azure

Этот раздел охватывает развертывание виртуальной машины, оснащенной ультра-диском в качестве диска данных. Предполагается, что вы знакомы с развертыванием виртуальной машины, [Quickstart: Create a Windows virtual machine in the Azure portal](../articles/virtual-machines/windows/quick-create-portal.md)если вы этого не сделаете, см.

- Войдите на [портал Azure](https://portal.azure.com/) и перейдите к развертыванию виртуальной машины (VM).
- Убедитесь в том, чтобы выбрать [поддерживаемый размер ИТ и региона.](#ga-scope-and-limitations)
- Выберите **зону доступности** в **вариантах доступности.**
- Заполните оставшиеся записи с выбором по вашему выбору.
- Выберите **Диски**.

![создать ультра-диск с поддержкой vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- На лезвии дисков выберите **Yes** для **совместимости Включаемultra Disk.**
- Выберите **Создать и прикрепить новый диск,** чтобы прикрепить ультра диск сейчас.

![включить и прикрепить-ультра-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- На **создайте новый** дисклезное лезвие, введите имя, а затем выберите **размер изменения.**
- Измените **тип учетной записи** на **Ultra Disk.**
- Измените значения **размера пользовательского диска (GiB),** **диска IOPS**и **пропускной перевалки диска** на те, которые вы высоводим.
- Выберите **OK** в обоих лезвиях.
- Продолжить развертывание VM, это будет так же, как вы бы развернуть любой другой VM.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Прикрепите ультрадиск с помощью портала Azure

Кроме того, если ваш существующий VM находится в зоне региона/доступности, которая способна использовать ультрадиски, вы можете использовать ультра диски без необходимости создавать новый VM. Включив ультра диски на существующий VM, а затем прикрепить их в качестве дисков данных.

- Перейдите к VM и выберите **диски.**
- Выберите **Изменить**.

![варианты-селектор-ультра-диски.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Выберите **Да** для **совместимости Свативного диска.**

![ультра-варианты-да-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Щелкните **Сохранить**.
- Выберите **Добавить диск данных,** а затем в выпадении для **имя** выберите **Создать диск.**

![создать-и-прикрепить-новый-ультра-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Заполните имя для нового диска, а затем выберите **размер изменения.**
- Измените **тип учетной записи** на **Ultra Disk.**
- Измените значения **размера пользовательского диска (GiB),** **диска IOPS**и **пропускной перевалки диска** на те, которые вы высоводим.
- Выберите **OK,** затем выберите **Создать**.

![изготовление-новый-ультра-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- После того, как вы вернетесь на лезвие диска, выберите **Сохранить**.

![экономия и крепления-новый-ультра-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Отрегулируйте производительность ультрадиска с помощью портала Azure

Ультра диски предлагают уникальную возможность, которая позволяет регулировать их производительность. Эти настройки можно внести с портала Azure на самих дисках.

- Перейдите к VM и выберите **диски.**
- Выберите ультра диск, который вы хотите изменить.

![выбор-ультра-диск-к-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Выберите **Конфигурацию,** а затем внести изменения.
- Щелкните **Сохранить**.

![настройка-ультра-диск-производительность-и-размер.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Развертывание ультра-диска с помощью CLI

Во-первых, определите размер VM для развертывания. Ознакомьтесь с разделом [области и ограничений GA](#ga-scope-and-limitations) для списка поддерживаемых размеров VM.

Вы должны создать VM, который способен использовать ультра диски, для того, чтобы прикрепить ультра диск.

Замените или установите **$vmname,** **$rgname,** **$diskname,** **$location,** **$password,** **$user** переменные с вашими собственными значениями. Установите **$zone** значение зоны доступности, которую вы получили с [самого начала этой статьи.](#determine-vm-size-and-region-availability) Затем запустите следующую команду CLI, чтобы создать ультра включенный VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Включить ультра дисковую совместимость на существующем VM

Если ваш VM соответствует требованиям, изложенным в [области GA и ограничениях](#ga-scope-and-limitations) и находится в [соответствующей зоне для вашей учетной записи,](#determine-vm-size-and-region-availability)то вы можете включить ультра диск совместимости на вашем VM.

Для обеспечения совместимости ультра дисков необходимо остановить VM. После остановки VM, вы можете включить совместимость, прикрепить ультра диск, а затем перезапустить VM:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Создание ультра-диска с помощью CLI

Теперь, когда у вас есть VM, который способен прикреплять ультра диски, вы можете создать и прикрепить ультра диск к нему.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Прикрепите ультрадиск к VM с помощью CLI

Кроме того, если ваш существующий VM находится в зоне региона/доступности, которая способна использовать ультрадиски, вы можете использовать ультра диски без необходимости создавать новый VM.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Отрегулируйте производительность ультрадиска с помощью CLI

Ультра диски предлагают уникальную возможность, которая позволяет настроить их производительность, следующая команда изображает, как использовать эту функцию:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Развертывание ультра-диска с помощью PowerShell

Во-первых, определите размер VM для развертывания. Ознакомьтесь с разделом [области и ограничений GA](#ga-scope-and-limitations) для списка поддерживаемых размеров VM.

Чтобы использовать ультра диски, необходимо создать VM, который способен использовать ультра диски. Замените или установите **$resourcegroup** и **$vmName** переменные с вашими собственными значениями. Установите **$zone** значение зоны доступности, которую вы получили с [самого начала этой статьи.](#determine-vm-size-and-region-availability) Затем запустите следующую команду [New-AzVm,](/powershell/module/az.compute/new-azvm) чтобы создать ультра включенный VM:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Включить ультра дисковую совместимость на существующем VM

Если ваш VM соответствует требованиям, изложенным в [области GA и ограничениях](#ga-scope-and-limitations) и находится в [соответствующей зоне для вашей учетной записи,](#determine-vm-size-and-region-availability)то вы можете включить ультра диск совместимости на вашем VM.

Для обеспечения совместимости ультра дисков необходимо остановить VM. После остановки VM, вы можете включить совместимость, прикрепить ультра диск, а затем перезапустить VM:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Создание ультра-диска с помощью PowerShell

Теперь, когда у вас есть VM, который способен использовать ультра диски, вы можете создать и прикрепить ультра диск к нему:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Прикрепите ультрадиск к VM с помощью PowerShell

Кроме того, если ваш существующий VM находится в зоне региона/доступности, которая способна использовать ультрадиски, вы можете использовать ультра диски без необходимости создавать новый VM.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Отрегулируйте производительность ультрадиска с помощью PowerShell

Ультра диски имеют уникальную возможность, которая позволяет регулировать их производительность, следующая команда является примером, который регулирует производительность без отсоединить диск:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```