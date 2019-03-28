---
title: Руководство. Управление дисками Azure с помощью Azure PowerShell | Документация Майкрософт
description: В этом руководстве объясняется, как создать и администрировать диски Azure для виртуальных машин с помощью PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: be6375972747c5c962bf1a8ef9b9b0093402bb7a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368261"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Руководство по управлению дисками Azure с помощью Azure PowerShell

Виртуальные машины Azure хранят операционную систему, приложения и данные на дисках. При создании виртуальной машины важно выбрать размер диска и конфигурацию, которые будут соответствовать ожидаемой рабочей нагрузкой. В этом руководстве рассматривается развертывание дисков виртуальных машин и управление ими. Здесь содержатся сведения о:

> [!div class="checklist"]
> * дисках ОС и временных дисках;
> * Диски данных
> * дисками уровня "Стандартный" и "Премиум";
> * производительностью дисков;
> * присоединением и подготовкой дисков данных;

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="default-azure-disks"></a>Диски Azure по умолчанию

При создании виртуальной машины Azure к ней автоматически подключаются два диска. 

**Диск операционной системы**. Размер дисков операционной системы может составлять до 4 ТБ. Это диски, содержащие операционную систему виртуальной машины.  Диску ОС по умолчанию назначается буква *C:*. Конфигурация кэширования диска ОС оптимизирована для производительности операционной системы. На диске ОС **не должны** размещаться приложения или данные. Для приложений и данных используйте диск данных, который описан далее в этой статье.

**Временный диск**. Временные диски используют твердотельные накопители, расположенные на том же узле Azure, что и виртуальная машина. Временные диски обладают высокой производительностью и могут быть использованы для таких операций, как обработка временных данных. Тем не менее, если виртуальная машина перемещается на новый узел, удаляются все данные, хранящиеся на временном диске. Размер временного диска определяется [размером виртуальной машины](sizes.md). Временным дискам по умолчанию назначается буква *D:*.

## <a name="azure-data-disks"></a>Диски данных Azure

Вы можете добавить дополнительные диски данных, чтобы установить приложения и хранить данные. Диски данных следует использовать в любой ситуации, когда требуется надежное хранилище данных, обеспечивающее высокую скорость реагирования. Максимальная емкость каждого диска данных составляет 4 ТБ. Размер виртуальной машины определяет, сколько дисков данных можно к ней подключить. Для каждого виртуального ЦП виртуальной машины можно подключить четыре диска данных.

## <a name="vm-disk-types"></a>Типы дисков виртуальной машины

В Azure предоставляются диски двух типов.

**Диски ценовой категории "Стандартный"**. Это жесткие диски, которые обеспечивают экономичность хранения данных и достаточную производительность. Эти диски идеально подходят для экономных рабочих нагрузок разработки и тестирования.

**Диски ценовой категории "Премиум"**. Это высокопроизводительные твердотельные накопители с низкой задержкой. Они идеально подходят для виртуальных машин, выполняющих производственную рабочую нагрузку. Хранилище уровня "Премиум" поддерживает виртуальные машины серий DS, DSv2, GS и FS. Диски уровня "Премиум" бывают пяти типов: P10, P20, P30, P40, P50. Размер диска определяет его тип. При выборе размер диска округляется в большую сторону до следующего типа. Например, размерам диска до 128 ГБ, от 129 до 512 ГБ и больше 512 ГБ соответствуют типы Р10, Р20 и P30.

### <a name="premium-disk-performance"></a>Производительность диска уровня "Премиум"
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Хотя в таблице выше указано максимальное число операций ввода-вывода в секунду на диск, можно обеспечить более высокий уровень производительности, применив чередование нескольких дисков данных. Например, к виртуальной машине Standard_GS5 можно подключить 64 диска данных. Если каждый из этих дисков относится к размеру P30, можно добиться производительности, достигающей 80 000 операций ввода-вывода в секунду. Дополнительные сведения о максимальных количествах операций ввода-вывода в секунду для виртуальных машин см. в статье [Размеры виртуальных машин Windows в Azure](./sizes.md).

## <a name="create-and-attach-disks"></a>Создание и подключение дисков

Для выполнения примера в этом руководстве требуется виртуальная машина. При необходимости создайте виртуальную машину с помощью следующих команд.

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Создайте виртуальную машину с помощью команды [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Вам будет предложено ввести имя пользователя и пароль для учетной записи администратора виртуальной машины.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Создайте начальную конфигурацию, выполнив команду [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). В следующем примере настраивается диск размером 128 ГБ.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Создайте диск данных с помощью команды [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Получите виртуальную машину, в которую вы хотите добавить диск данных, выполнив команду [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Добавьте диск данных в конфигурацию виртуальной машины с помощью команды [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Обновите виртуальную машину с помощью команды [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Подготовка дисков данных

После подключения диска к виртуальной машине нужно настроить операционную систему для его использования. В примере ниже показано, как вручную настроить первый диск, добавляемый в виртуальную машину. Этот процесс можно автоматизировать с помощью [расширения настраиваемых сценариев](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Настройка вручную

Создайте RDP-подключение к виртуальной машине. Откройте PowerShell и выполните этот сценарий.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Проверка диска данных

Чтобы убедиться, что диск данных подключен, просмотрите `StorageProfile` для подключенного диска `DataDisks`.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Результат должен выглядеть примерно так:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы ознакомились с дисками виртуальных машин, а именно с:

> [!div class="checklist"]
> * дисками ОС и временными дисками;
> * Диски данных
> * дисками уровня "Стандартный" и "Премиум";
> * производительностью дисков;
> * присоединением и подготовкой дисков данных.

Перейдите к следующему руководству, чтобы узнать об автоматической настройке виртуальных машин.

> [!div class="nextstepaction"]
> [Автоматизация настройки виртуальной машины](./tutorial-automate-vm-deployment.md)
