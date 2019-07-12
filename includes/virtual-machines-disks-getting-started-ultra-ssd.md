---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 742e0028b1f92beb8300cc97f09d8292259fbc0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712576"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Включите и развернете Azure ultra SSD (Предварительная версия)

Azure ultra твердотельные накопители, предложение (SSD) (Предварительная версия): высокая пропускная способность, высокий уровень операций ввода-ВЫВОДА и постоянно низкую задержку хранилище дисков для виртуальных машин (ВМ) Azure IaaS. Это новое предложение обеспечивает первоклассную производительность с тем же уровнем доступности, что и имеющиеся предложения дисков. Одним из крупных достоинств ultra SSDs является возможность динамически изменить производительность SSD, а также рабочих нагрузок без необходимости перезагрузки виртуальных машин. Ultra SSDs подходят для ресурсоемких рабочих нагрузок, таких как SAP HANA, баз данных верхнего уровня и рабочих нагрузок с интенсивными транзакции.

В настоящее время ultra SSDs доступны в предварительной версии, а также [регистрации](https://aka.ms/UltraSSDPreviewSignUp) в предварительной версии, чтобы получить к ним доступ.

## <a name="determine-your-availability-zone"></a>Определить зону доступности

После утверждения, необходимо определить требуемую зону доступности, вы находитесь в, чтобы использовать ultra SSDs. Выполните одну из следующих команд, чтобы определить требуемую зону в восточной части США 2, для развертывания на ultra диск, чтобы:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

Ответ будет выглядеть так, чтобы формы ниже, где X — зоны, которые будут использоваться для развертывания в восточной части США 2. Значением X может иметь 1, 2 или 3.

Сохранить **зоны** значение, он представляет зону доступности, и он понадобится для развертывания ultra диске SSD.

|ResourceType  |Имя  |Местоположение  |Зоны  |Ограничение  |Функция  |Значение  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Если не был получен ответ из команды, то регистрацию, чтобы эта функция по-прежнему является либо ожидающие или не утвержденные еще.

Теперь, определив зону для развертывания, выполните соответствующие инструкции в этой статье, чтобы развернуть свою первую виртуальную машину с диском SSD (цен. категория "Ультра").

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Развертывание ultra диске SSD с помощью Azure Resource Manager

Во-первых Определите размер виртуальной Машины для развертывания. В рамках этой предварительной версии поддерживаются только семейства виртуальных машин DsV3 и EsV3. Обратитесь ко второй таблице в этом [блоге](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/), чтобы получить дополнительные сведения об этих размерах виртуальных машин.

Если вы хотите создать виртуальную Машину с несколькими ultra SSDs, см. пример [создать виртуальную Машину с несколькими ultra SSD](https://aka.ms/UltraSSDTemplate).

Если вы планируете использовать собственный шаблон, убедитесь, что **apiVersion** для `Microsoft.Compute/virtualMachines` и `Microsoft.Compute/Disks` задается как `2018-06-01` (или более поздней версии).

Значение sku диска **UltraSSD_LRS**, затем установите емкости дисков, операций ввода-ВЫВОДА, зона доступности и пропускной способности в Мбит/с, чтобы создать ultra диск.

После подготовки виртуальной машины можно будет секционировать и отформатировать ее диски данных, а затем настроить их для рабочих нагрузок.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Развертывание ultra диске SSD с помощью интерфейса командной строки

Во-первых Определите размер виртуальной Машины для развертывания. В рамках этой предварительной версии поддерживаются только семейства виртуальных машин DsV3 и EsV3. Обратитесь ко второй таблице в этом [блоге](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/), чтобы получить дополнительные сведения об этих размерах виртуальных машин.

Чтобы использовать ultra SSDs, необходимо создать виртуальную Машину, которая может использовать ultra SSDs.

Замените или задать **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** переменных собственными значениями. Задайте **$zone** равным зону доступности, полученный из [начало в этой статье](#determine-your-availability-zone). Затем выполните следующую команду интерфейса командной строки, чтобы создать виртуальную Машину ultra включена:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Создание ultra диске SSD с помощью интерфейса командной строки

Теперь, когда у вас есть виртуальную Машину, которая может использовать ultra SSDs, можно создать и присоединить к ней ultra диске SSD.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Настроить производительность ultra диске SSD с помощью интерфейса командной строки

Ultra SSD обеспечивают уникальные возможности, можно настроить их производительность, команду ниже описывается, как использовать эту функцию:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Развертывание ultra диске SSD с помощью PowerShell

Во-первых Определите размер виртуальной Машины для развертывания. В рамках этой предварительной версии поддерживаются только семейства виртуальных машин DsV3 и EsV3. Обратитесь ко второй таблице в этом [блоге](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/), чтобы получить дополнительные сведения об этих размерах виртуальных машин.

Чтобы использовать ultra SSDs, необходимо создать виртуальную Машину, которая может использовать ultra SSDs. Замените или задать **$resourcegroup** и **$vmName** переменных собственными значениями. Задайте **$zone** равным зону доступности, полученный из [начало в этой статье](#determine-your-availability-zone). Затем выполните следующую команду, [командлета New-AzVm](/powershell/module/az.compute/new-azvm) виртуальной Машины с включенным команду, чтобы создать ultra:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Создание ultra диске SSD с помощью PowerShell

Теперь, когда у вас есть виртуальную Машину, которая может использовать ultra SSDs, можно создать и присоединить к ней ultra диске SSD:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Настроить производительность ultra диске SSD с помощью PowerShell

Ultra SSDs имеют уникальные возможности, можно настроить их производительность, команду ниже приведен пример, который настраивает производительность без необходимости отключить диск:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Следующие шаги

Если вы хотите попробовать новый тип диска [запросить доступ к предварительной версии в этом опросе](https://aka.ms/UltraSSDPreviewSignUp).