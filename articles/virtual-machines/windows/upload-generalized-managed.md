---
title: Создание управляемой виртуальной машины Azure на основе универсальных локальных дисков VHD | Документация Майкрософт
description: Отправка универсального диска VHD в Azure и создание виртуальных машин с его помощью в модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ead44c321ffb7afb69295d1bf8c0e1acd26cb9ce
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749071"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure

В этой статье описывается, как с помощью PowerShell отправить VHD универсальной виртуальной машины в Azure, создать образ на основе VHD и виртуальную машину на основе этого образа. Вы можете отправить диск VHD, экспортированный из локального инструмента виртуализации или из другого облака. При использовании [Управляемых дисков](managed-disks-overview.md) для новой виртуальной машины упрощается управление виртуальными машинами и обеспечивается более высокий уровень доступности виртуальной машины, размещенной в группе доступности. 

Если необходим пример сценария, см. статью [Пример сценария для отправки VHD в Azure и создания виртуальной машины](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Перед началом работы

- Прежде чем передавать в Azure какой-либо виртуальный жесткий диск, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Прежде чем начать миграцию на [Управляемые диски](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks), внимательно изучите [этот раздел](managed-disks-overview.md).

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Подготовка исходной виртуальной машины к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Дополнительные сведения о Sysprep приведены в [обзоре Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в разделе [Поддержка ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Если вы хотите запустить Sysprep перед отправкой виртуального жесткого диска в Azure в первый раз, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на %windir%\system32\sysprep и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запустите Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.


## <a name="upload-the-vhd-to-your-storage-account"></a>Передача виртуального жесткого диска в учетную запись хранения

Теперь виртуальный жесткий диск можно передать прямо в управляемый диск. Инструкции см. в статье [Отправка виртуального жесткого диска в Azure с помощью Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Создание управляемого образа на основе переданного виртуального жесткого диска 

Создание управляемого образа на основе обобщенного управляемого диска ОС. Подставьте собственные значения.


Сначала задайте некоторые параметры.

```powershell
$location = "East US" 
$imageName = "myImage"
```

Создайте образ с помощью универсального виртуального жесткого диска ОС.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
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
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Дальнейшие действия

Войдите на свою новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

