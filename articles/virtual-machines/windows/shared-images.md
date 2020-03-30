---
title: Создание общих VM-изображений с Azure PowerShell
description: Узнайте, как использовать Azure PowerShell для создания общего образа виртуальной машины в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: db877c96167fc011c1a8bd52cc1d0b63260007c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74066237"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Создание коллекции общих образов с помощью Azure PowerShell 

[Коллекция общих образов](shared-image-galleries.md) упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки задач развертывания, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Коллекция общих образов позволяет предоставлять общий доступ к пользовательским образам виртуальной машины другим пользователям вашей организации в пределах региона или между регионами в клиенте AAD. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. Вы можете создать несколько коллекций, чтобы можно было логически группировать общие образы. 

Коллекция — это ресурс верхнего уровня, который обеспечивает полное управление доступом на основе ролей (RBAC). Образы могут быть версионными, и вы можете копировать каждую версию образа в другой набор регионов Azure. Коллекция работает только с Управляемыми образами.

Функция коллекции общих образов имеет несколько типов ресурсов. Мы будем использовать или создавать в этой статье следующие ресурсы:

| Ресурс | Описание|
|----------|------------|
| **Управляемое изображение** | Это базовый образ, который можно использовать отдельно или для создания **версии образа** в коллекции образов. Управляемые образы создаются на основе обобщенных виртуальных машин. Управляемый образ — это специальный тип виртуального жесткого диска, который может применяться для создания нескольких виртуальных машин. Теперь его можно использовать для создания версий общих образов. |
| **Фотогалерея** | Как и Azure Marketplace, **коллекция образов** — это репозиторий для управления и совместного использования образов, но в отличие от Azure Marketplace доступ к коллекции образов контролируете вы. |
| **Определение образа** | Образы определяются в пределах коллекции и содержат в себе сведения об образе и требования для его использования. Эти сведения включают в себя: определение, относится ли этот образ к Windows или к Linux, заметки о выпуске, а также минимальные и максимальные требования к памяти. Это определение типа образа. |
| **Версия образа** | **Версия образа** используется для создания виртуальной машины с помощью коллекции. В зависимости от требований для вашей среды, у вас может быть несколько версий образа. Так же как и управляемый образ при использовании **версии образа** для создания виртуальной машины, версия образа используется для создания новых дисков для виртуальной машины. Версии образов можно использовать несколько раз. |

Для каждых 20 ВМ, которые вы создаете одновременно, мы рекомендуем вам сохранить одну реплику. Например, если вы создаете 120 вм: одновременно с использованием одного и того же изображения в регионе, мы предлагаем вам сохранить по крайней мере 6 копий изображения. Для получения дополнительной [информации см.](/azure/virtual-machines/windows/shared-image-galleries#scaling)

## <a name="before-you-begin"></a>Перед началом

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ. Вы можете следить за [учебником: Создайте пользовательское изображение VM Azure с Azure PowerShell,](tutorial-custom-images.md) чтобы создать его, если это необходимо. Если управляемое изображение содержит диск данных, размер диска данных не может быть более 1 ТБ.

При работе со статьей по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Создание виртуальных машин из образа

Когда версия образа будет готова, вы можете создать одну или несколько виртуальных машин. Использование [смдлета New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

Этот пример создает VM под названием *myVMfromImage*, в *myResourceGroup* в центре обработки данных *в южной части Центральной США.*


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Дальнейшие действия
[Azure Image Builder (предварительный просмотр)](image-builder-overview.md) может помочь автоматизировать создание версии изображений, вы даже можете использовать его для обновления и [создания новой версии изображения из существующей версии изображения.](image-builder-gallery-update-image-version.md) 

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание Общей коллекции образов.](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Создание виртуальной машины из версии образа](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](troubleshooting-shared-images.md).

