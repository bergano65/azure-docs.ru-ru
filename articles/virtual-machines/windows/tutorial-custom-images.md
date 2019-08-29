---
title: Руководство. Создание пользовательских образов виртуальных машин с помощью Azure PowerShell | Документация Майкрософт
description: В этом руководстве описано, как с помощью Azure PowerShell создать пользовательский образ виртуальной машины Linux в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 24a382680860890e57c8d5a380b8a1bb097baaa1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101688"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Руководство по Создание пользовательского образа виртуальной машины Azure с помощью Azure PowerShell

Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки развертываний и обеспечения согласованности между несколькими виртуальными машинами. В рамках этого руководства вы создадите собственный пользовательский образ виртуальной машины Azure с помощью PowerShell. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка виртуальной машины к использованию с помощью Sysprep
> * Создание пользовательского образа
> * Создание виртуальной машины из пользовательского образа
> * Получение списка всех образов в подписке
> * удалять образ.

Мы предлагаем общедоступную предварительную версию службы [построителя образов виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). Просто опишите настройки в шаблоне, и он автоматически выполнит шаги по созданию образа, описанные в этой статье. [Попробуйте построитель образов Azure (предварительная версия)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Перед началом работы

Ниже подробно описано, как преобразовать существующую виртуальную машину в многократно используемый пользовательский образ, на основе которого можно создавать экземпляры виртуальной машины.

Для выполнения примера в этом руководстве требуется виртуальная машина. Этот [пример сценария](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) позволяет создать ее при необходимости. При работе с примером по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="prepare-vm"></a>Подготовка виртуальной машины

Чтобы создать образ виртуальной машины, нужно подготовить исходную виртуальную машину к использованию, отменив ее распределение и пометив ее как универсальную в Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Дополнительные сведения о Sysprep см. в статье [How to Use Sysprep: An Introduction (Использование средства Sysprep: введение). статье](https://technet.microsoft.com/library/bb457073.aspx).


1. Подключитесь к виртуальной машине.
2. Откройте окно командной строки с правами администратора. Измените каталог на *%windir%\system32\sysprep* и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы** и нажмите кнопку **ОК**.
5. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. **Не перезапускайте виртуальную машину.**

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Отмена выделения и пометка виртуальной машины как обобщенной

Чтобы создать образ виртуальной машины, необходимо отменить ее выделение и пометить ее как универсальную в Azure.

Отмените распределение виртуальной машины с помощью командлета [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Задайте для виртуальной машины состояние `-Generalized`, выполнив командлет [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Создание образа

Теперь можно создать образ виртуальной машины с помощью командлетов [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) и [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). В следующем примере создается образ *myImage* из виртуальной машины *myVM*.

Получите виртуальную машину. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Создайте конфигурацию образа.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Создайте образ.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Создание виртуальных машин из образа

Теперь, когда образ готов, из него можно создать одну или несколько виртуальных машин. Создание виртуальной машины из образа похоже на создание виртуальной машины с помощью образа Marketplace. При использовании образа Marketplace требуется указать сведения об образе, его поставщике, предложении, номере SKU и версии. С помощью упрощенного набора параметров для командлета [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) укажите имя настраиваемого образа, так как он находится в той же группе ресурсов. 

В этом примере создается виртуальная машина *myVMfromImage* из образа *myImage* в группе ресурсов *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Управление образами 

Ниже приведены некоторые примеры распространенных задач управления образами, а также способы их выполнения с помощью PowerShell.

Вывод списка всех образов по имени.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Удаление образа. В этом примере из *myResourceGroup* удаляется образ с именем *myImage*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы создали пользовательский образ виртуальной машины. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка виртуальной машины к использованию с помощью Sysprep
> * Создание пользовательского образа
> * Создание виртуальной машины из пользовательского образа
> * Получение списка всех образов в подписке
> * удалять образ.

В следующем руководстве описано, как создавать виртуальные машины высокого уровня доступности.

> [!div class="nextstepaction"]
> [Создание высокодоступных виртуальных машин](tutorial-availability-sets.md)



