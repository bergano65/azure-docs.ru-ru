---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 838037804baad9105b4636934de957c2e5f3e810
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612155"
---
# <a name="using-azure-ultra-disks"></a>Использование Ultra дисков Azure

Высокопроизводительные диски Azure обеспечивают высокую пропускную способность, высокую скорость операций ввода-вывода и постоянную задержку на диске для виртуальных машин Azure IaaS. Это новое предложение обеспечивает первоклассную производительность с тем же уровнем доступности, что и имеющиеся предложения дисков. Одним из основных преимуществ использования Ultra Disks является возможность динамического изменения производительности SSD вместе с рабочими нагрузками без необходимости перезапуска виртуальных машин. Ultra Disks подходит для ресурсоемких рабочих нагрузок, таких как SAP HANA, базы данных верхнего уровня и рабочие нагрузки с большим объемом транзакций.

## <a name="ga-scope-and-limitations"></a>Область и ограничения общедоступного уровня

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Определение размера виртуальной машины и доступности регионов

Чтобы использовать диски Ultra, необходимо определить, в какой зоне доступности вы используете. Не каждый регион поддерживает все размеры виртуальных машин с Ultra Disks. Чтобы определить, поддерживает ли ваш регион, зону и размер виртуальной машины, выполните одну из следующих команд, не забудьте сначала заменить значения **Region**, **vmSize**и **Subscription** .

CLI:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Ответ будет похож на приведенный ниже формат, где X — это зона, используемая для развертывания в выбранном регионе. Значением X может иметь 1, 2 или 3.

Сохраните значение **зон** , оно представляет вашу зону доступности и потребуется для развертывания Ultra Disk.

|ResourceType  |Имя  |Расположение  |Зоны  |Ограничение  |Функция  |Значение  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Если команда не ответила, выбранный размер виртуальной машины не поддерживается для Ultra Disks в выбранном регионе.

Теперь, когда вы узнали, какая зона будет развертываться, выполните действия по развертыванию, описанные в этой статье, чтобы развернуть виртуальную машину с подключенным Ultra Disk или подключить к существующей виртуальной машине Ultra Disk.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Развертывание Ultra Disk с помощью Azure Resource Manager

Сначала определите размер виртуальной машины для развертывания. Список поддерживаемых размеров виртуальных машин см. в разделе общедоступная [область и ограничения](#ga-scope-and-limitations) .

Если вы хотите создать виртуальную машину с несколькими Ultra-дисками, ознакомьтесь с примером [Создание виртуальной машины с несколькими Ultra дисками](https://aka.ms/ultradiskArmTemplate).

Если вы планируете использовать собственный шаблон, убедитесь, что для параметра **apiVersion** для `Microsoft.Compute/virtualMachines` и `Microsoft.Compute/Disks` задано значение `2018-06-01` (или более поздней).

Задайте для номера SKU диска значение **UltraSSD_LRS**, а затем установите емкость диска, число операций ввода-вывода, зону доступности и пропускную способность в Мбит/с для создания диска Ultra.

После подготовки виртуальной машины можно будет секционировать и отформатировать ее диски данных, а затем настроить их для рабочих нагрузок.

## <a name="deploy-an-ultra-disk-using-cli"></a>Развертывание Ultra Disk с помощью интерфейса командной строки

Сначала определите размер виртуальной машины для развертывания. Список поддерживаемых размеров виртуальных машин см. в разделе общедоступная [область и ограничения](#ga-scope-and-limitations) .

Для подключения Ultra Disk необходимо создать виртуальную машину, которая может использовать диски Ultra.

Замените или установите **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password**, **$User** переменные собственными значениями. Задайте **$Zone** в качестве значения вашей зоны доступности, полученной в [начале этой статьи](#determine-vm-size-and-region-availability). Затем выполните следующую команду CLI, чтобы создать виртуальную машину с Ultra Enabled.

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Создание Ultra Disk с помощью интерфейса командной строки

Теперь, когда у вас есть виртуальная машина, которая поддерживает подключение Ultra Disks, можно создать и подключить к ней Ultra Disk.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Подключение Ultra Disk к виртуальной машине с помощью интерфейса командной строки

Кроме того, если существующая виртуальная машина находится в зоне региона или доступности, которая поддерживает использование Ultra Disks, можно использовать Ultra Disks без необходимости создавать новую виртуальную машину.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Настройка производительности Ultra Disk с помощью интерфейса командной строки

Ultra Disks предлагает уникальную возможность, позволяющую настроить их производительность. в следующей команде показано, как использовать эту функцию:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Развертывание Ultra Disk с помощью PowerShell

Сначала определите размер виртуальной машины для развертывания. Список поддерживаемых размеров виртуальных машин см. в разделе общедоступная [область и ограничения](#ga-scope-and-limitations) . Дополнительные сведения об этих размерах виртуальных машин.

Чтобы использовать диски Ultra, необходимо создать виртуальную машину, которая может использовать диски Ultra. Замените или задайте переменные **$resourcegroup** и **$vmName** собственными значениями. Задайте **$Zone** в качестве значения вашей зоны доступности, полученной в [начале этой статьи](#determine-vm-size-and-region-availability). Затем выполните следующую команду [New-AzVm](/powershell/module/az.compute/new-azvm) , чтобы создать виртуальную машину с Ultra Enabled.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Создание Ultra Disk с помощью PowerShell

Теперь, когда у вас есть виртуальная машина, способная использовать диски Ultra, вы можете создать и подключить к ней Ultra Disk:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Подключение Ultra Disk к виртуальной машине с помощью PowerShell

Кроме того, если существующая виртуальная машина находится в зоне региона или доступности, которая поддерживает использование Ultra Disks, можно использовать Ultra Disks без необходимости создавать новую виртуальную машину.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Настройка производительности Ultra Disk с помощью PowerShell

У дисков Ultra есть уникальные возможности, позволяющие настроить их производительность. ниже приведен пример, в котором выполняется настройка производительности без отключения диска.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите попробовать новый тип диска [запросить доступ с помощью этого опроса](https://aka.ms/UltraDiskSignup).