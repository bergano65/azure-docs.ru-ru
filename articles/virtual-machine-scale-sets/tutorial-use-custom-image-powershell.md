---
title: Руководство. Использование пользовательского образа виртуальной машины в масштабируемом наборе с помощью Azure PowerShell | Документация Майкрософт
description: Сведения о создании пользовательского образа виртуальной машины, который можно использовать для развертывания масштабируемого набора виртуальных машин, с помощью Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a3b0f9b2b158bd36259ee96633682e1777333499
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981049"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Руководство. Создание и использование пользовательского образа для масштабируемых наборов виртуальных машин с помощью Azure PowerShell

Создавая масштабируемый набор, вы указываете образ для использования при развертывании экземпляров виртуальных машин. Чтобы сократить количество задач после развертывания экземпляров виртуальных машин, можно использовать пользовательский образ виртуальной машины. Этот образ содержит все необходимые установки или конфигурации приложения. Все экземпляры виртуальных машин, созданные в масштабируемом наборе, используют пользовательский образ виртуальной машины и готовы обслуживать трафик приложения. Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание и настройка виртуальной машины;
> * отмена подготовки виртуальной машины и подготовка ее к использованию;
> * создание пользовательского образа виртуальной машины из исходной виртуальной машины;
> * развертывание масштабируемого набора, использующего пользовательский образ виртуальной машины.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="create-and-configure-a-source-vm"></a>Создание и настройка исходной виртуальной машины

>[!NOTE]
> В этом руководстве приведены пошаговые инструкции по созданию и использованию универсального образа виртуальной машины. Создание масштабируемого набора на основе специализированного образа VHD не поддерживается.

Сначала создайте группу ресурсов, выполнив командлет [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), а затем создайте виртуальную машину с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm). Позже эта виртуальная машина будет использована в качестве источника для пользовательского образа. В следующем примере создается виртуальная машина с именем *myCustomVM* в группе ресурсов *myResourceGroup*. При появлении запроса введите имя пользователя и пароль в качестве учетных данных для входа на виртуальную машину:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Чтобы подключиться к виртуальной машине, получите общедоступный IP-адрес с помощью командлета [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), как показано ниже.

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Создайте удаленное подключение к виртуальной машине. Если вы используете Azure Cloud Shell, выполните этот шаг с помощью локальной командной строки PowerShell или клиента удаленного рабочего стола. Укажите собственный IP-адрес из предыдущей команды. При появлении запроса введите учетные данные, использованные при создании виртуальной машины на первом шаге.

```powershell
mstsc /v:<IpAddress>
```

Чтобы настроить виртуальную машину, установите базовый веб-сервер. Когда экземпляр виртуальной машины в масштабируемом наборе будет развернут, у него будут все пакеты, необходимые для запуска веб-приложения. Откройте командную строку PowerShell локально на виртуальной машине и установите веб-сервер IIS с помощью командлета [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature), как показано ниже.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Последним шагом является подготовка виртуальной машины к использованию в качестве пользовательского образа. Служебная программа Sysprep удаляет все сведения вашей личной учетной записи и конфигурации, а также сбрасывает виртуальную машину в исходное состояние для будущих развертываний. Дополнительные сведения см. в статье [How to Use Sysprep: An Introduction (Как использовать Sysprep: введение). статье](https://technet.microsoft.com/library/bb457073.aspx).

Чтобы подготовить виртуальную машину к использованию, запустите Sysprep и настройте виртуальную машину для первого запуска. По завершению укажите служебной программе Sysprep завершить работу виртуальной машины.

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Удаленное подключение к виртуальной машине автоматически закрывается, когда Sysprep завершает процесс и виртуальная машина отключается.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Создание пользовательского образа виртуальной машины из исходной виртуальной машины
Теперь на исходной виртуальной машине установлен веб-сервер IIS и она настроена. Создайте пользовательский образ виртуальной машины для использования с масштабируемым набором.

Чтобы создать образ, нужно отменить выделение виртуальной машины. Освободите виртуальную машину с помощью командлета [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Затем с помощью командлета [Set-AzVm](/powershell/module/az.compute/set-azvm) измените состояние виртуальной машины на Generalized (Готова к использованию), чтобы указать платформе Azure о том, что виртуальная машина готова к применению пользовательского образа. Создать образ можно только из подготовленной виртуальной машины.

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Освобождение и подготовка виртуальной машины к использованию может занять несколько минут.

Теперь создайте образ виртуальной машины с помощью командлетов [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) и [New-AzImage](/powershell/module/az.compute/new-azimage). В следующем примере создается образ *myImage* из виртуальной машины.

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Создание масштабируемого набора на основе пользовательского образа виртуальной машины
Теперь создайте масштабируемый набор с помощью командлета [New-AzVmss](/powershell/module/az.compute/new-azvmss), который использует параметр `-ImageName` для определения пользовательского образа виртуальной машины, созданного на предыдущем шаге. Чтобы распределить трафик между отдельными экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Подсистема балансировки нагрузки определяет правила передачи трафика на TCP-порт 80, а также разрешает подключение удаленного рабочего стола трафик через TCP-порт 3389 и удаленное взаимодействие PowerShell через TCP-порт 5985. При появлении запроса введите учетные данные администратора для экземпляров виртуальных машин в масштабируемом наборе:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="test-your-scale-set"></a>Проверка масштабируемого набора
Чтобы изучить работу масштабируемого набора, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress), как показано ниже.


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится веб-страница служб IIS по умолчанию, как показано ниже.

![Службы IIS, запущенные из пользовательского образа виртуальной машины](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы удалить масштабируемый набор и дополнительные ресурсы, удалите группу ресурсов и все входящие в нее ресурсы с помощью команды [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Параметр `-Force` подтверждает, что вы хотите удалить ресурсы без дополнительного запроса. При использовании параметра `-AsJob` управление возвращается в командную строку без ожидания завершения операции.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создавать и использовать пользовательский образ виртуальной машины для масштабируемых наборов с помощью Azure PowerShell, в частности, как выполнять такие задачи:

> [!div class="checklist"]
> * создание и настройка виртуальной машины;
> * отмена подготовки виртуальной машины и подготовка ее к использованию;
> * создание пользовательского образа виртуальной машины;
> * развертывание масштабируемого набора, использующего пользовательский образ виртуальной машины.

Перейдите к следующему руководству, чтобы узнать, как развертывать приложения в масштабируемый набор.

> [!div class="nextstepaction"]
> [Руководство по развертыванию приложений в масштабируемых наборах](tutorial-install-apps-powershell.md)
