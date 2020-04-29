---
title: Использование образов общих виртуальных машин для создания масштабируемого набора в Azure
description: Узнайте, как с помощью Azure PowerShell создать общие образы виртуальных машин для развертывания масштабируемых наборов виртуальных машин в Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368733"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Создание и использование общих образов для масштабируемых наборов виртуальных машин с помощью Azure PowerShell

Создавая масштабируемый набор, вы указываете образ для использования при развертывании экземпляров виртуальных машин. Служба "Коллекция общих образов" значительно упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Коллекция общих образов позволяет предоставлять общий доступ к пользовательским образам виртуальной машины другим пользователям вашей организации в пределах региона или между регионами в клиенте AAD. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. Вы можете создать несколько коллекций, чтобы можно было логически группировать общие образы. 

Коллекция — это ресурс верхнего уровня, который обеспечивает полное управление доступом на основе ролей (RBAC). Образы могут быть версионными, и вы можете копировать каждую версию образа в другой набор регионов Azure. Коллекция работает только с Управляемыми образами. 

Функция коллекции общих образов имеет несколько типов ресурсов. Мы будем использовать или создавать в этой статье следующие ресурсы:

| Ресурс | Описание|
|----------|------------|
| **Управляемый образ** | Это базовый образ, который можно использовать отдельно или для создания **версии образа** в коллекции образов. Управляемые образы создаются на основе обобщенных виртуальных машин. Управляемый образ — это специальный тип виртуального жесткого диска, который может применяться для создания нескольких виртуальных машин. Теперь его можно использовать для создания версий общих образов. |
| **Коллекция изображений** | Как и Azure Marketplace, **коллекция образов** — это репозиторий для управления и совместного использования образов, но в отличие от Azure Marketplace доступ к коллекции образов контролируете вы. |
| **Определение образа** | Образы определяются в пределах коллекции и содержат в себе сведения об образе и требования для его использования. Эти сведения включают в себя: определение, относится ли этот образ к Windows или к Linux, заметки о выпуске, а также минимальные и максимальные требования к памяти. Это определение типа образа. |
| **Версия образа** | **Версия образа** используется для создания виртуальной машины с помощью коллекции. В зависимости от требований для вашей среды, у вас может быть несколько версий образа. Так же как и управляемый образ при использовании **версии образа** для создания виртуальной машины, версия образа используется для создания новых дисков для виртуальной машины. Версии образов можно использовать несколько раз. |

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Перед началом

Ниже подробно описано, как преобразовать существующую виртуальную машину в многократно используемый пользовательский образ, на основе которого можно создавать экземпляры виртуальной машины.

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ. Вы можете следовать указаниям в [руководстве по созданию и использованию пользовательского образа для масштабируемых наборов виртуальных машин с Azure PowerShell](tutorial-use-custom-image-powershell.md) , чтобы создать его при необходимости. Если управляемый образ содержит диск данных, размер диска данных не может превышать 1 ТБ.

При работе со статьей по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Создание масштабируемого набора на основе версии общего образа

Создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzVmss](/powershell/module/az.compute/new-azvmss). В следующем примере создается масштабируемый набор на основе новой версии образа в *юго-центральном* центре обработки данных США. При появлении запроса задайте собственные административные учетные данные для экземпляров виртуальных машин в масштабируемом наборе:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Дальнейшие шаги

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание Общей коллекции образов.](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Создание виртуальной машины из версии образа](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](troubleshooting-shared-images.md).
