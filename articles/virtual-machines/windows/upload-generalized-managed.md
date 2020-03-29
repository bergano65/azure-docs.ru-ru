---
title: Создание VM из загруженного обобщенный VHD
description: Отправка универсального диска VHD в Azure и создание виртуальных машин с его помощью в модели развертывания Resource Manager.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464939"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure

В этой статье описывается, как с помощью PowerShell отправить VHD универсальной виртуальной машины в Azure, создать образ на основе VHD и виртуальную машину на основе этого образа. Вы можете отправить диск VHD, экспортированный из локального инструмента виртуализации или из другого облака. При использовании [Управляемых дисков](managed-disks-overview.md) для новой виртуальной машины упрощается управление виртуальными машинами и обеспечивается более высокий уровень доступности виртуальной машины, размещенной в группе доступности. 

Если необходим пример сценария, см. статью [Пример сценария для отправки VHD в Azure и создания виртуальной машины](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Перед началом

- Прежде чем передавать в Azure какой-либо виртуальный жесткий диск, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Прежде чем начать миграцию на [Управляемые диски](managed-disks-overview.md), внимательно изучите [этот раздел](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Подготовка исходной виртуальной машины к использованию с помощью Sysprep

Если вы еще не сделали этого, вам нужно Sysprep VM перед загрузкой VHD в Azure. Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Дополнительные сведения о Sysprep приведены в [обзоре Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Для получения дополнительной [информации](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)см.

> [!IMPORTANT]
> Если вы хотите запустить Sysprep перед отправкой виртуального жесткого диска в Azure в первый раз, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на %windir%\system32\sysprep и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.


## <a name="upload-the-vhd"></a>Отправка виртуального жесткого диска 

Теперь вы можете загрузить VHD прямо в управляемый диск. Для получения [инструкций см. Загрузите VHD в Azure с помощью Azure PowerShell.](disks-upload-vhd-to-managed-disk-powershell.md)



После того, как VHD будет загружен на управляемый диск, вам нужно использовать [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) для получения управляемого диска.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Создание образа
Создайте управляемое изображение с общего диска, управляемого ОС. Подставьте собственные значения.

Во-первых, установите некоторые переменные:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Создайте изображение с помощью управляемого диска.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Создайте образ.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Создание виртуальной машины

Теперь, когда образ готов, из него можно создать одну или несколько виртуальных машин. В этом примере создается виртуальная машина *myVM* из образа *myImage* в группе ресурсов *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Дальнейшие действия

Войдите на свою новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

