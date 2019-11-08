---
title: Добавление или удаление делегирования подсети в виртуальной сети Azure
titlesuffix: Azure Virtual Network
description: Узнайте, как добавить или удалить делегированную подсеть для службы в Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 340c9192b43cbcf2daacfb791d85135518dd970c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747887"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Добавление или удаление делегирования подсети

Делегирование подсети прямо разрешает службе в процессе развертывания создавать в подсети необходимые для этой службы ресурсы с помощью уникального идентификатора. В этой статье описывается, как добавить или удалить делегированную подсеть для службы Azure.

## <a name="portal"></a>Microsoft Azure

### <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

В этом разделе вы создадите виртуальную сеть и подсеть, которые позже будут делегироваться службе Azure.

1. Вверху с левой стороны экрана последовательно выберите **Создать ресурс** > **Сети** > **Виртуальная сеть**.
1. В подменю **Создать виртуальную сеть** введите или выберите следующую информацию:

    | Настройка | Значение |
    | ------- | ----- |
    | Имя | Введите *MyVirtualNetwork*. |
    | Пространство адресов | Введите *10.0.0.0/16*. |
    | Подписка | Выберите свою подписку.|
    | Группа ресурсов | Выберите **Создать**, а затем введите *myResourceGroup* и нажмите кнопку **ОК**. |
    | Расположение | Выберите **EastUS**.|
    | Имя подсети | Введите *mySubnet*. |
    | Диапазон адреса подсети | Введите *10.0.0.0/24*. |
    |||
1. Оставьте остальные значения по умолчанию и нажмите кнопку **создать**.

### <a name="permissions"></a>Разрешения

Если вы не создали подсеть, которую вы хотите делегировать службе Azure, вам потребуется следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`.

Встроенная роль « [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) » также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегирование подсети в службу Azure

В этом разделе вы делегируйте подсеть, созданную в предыдущем разделе, в службу Azure.

1. На портале в панели поиска введите *myVirtualNetwork*. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его.
2. В результатах поиска выберите *myVirtualNetwork*.
3. Выберите **подсети**, в разделе **Параметры**, а затем выберите **mySubnet**.
4. На странице *mySubnet* ( **Делегирование подсети** ) выберите из списка служб, указанных в разделе **Делегирование подсети службе** (например, **Microsoft. дбфорпостгрескл/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегирования подсети из службы Azure

1. На портале в панели поиска введите *myVirtualNetwork*. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его.
2. В результатах поиска выберите *myVirtualNetwork*.
3. Выберите **подсети**, в разделе **Параметры**, а затем выберите **mySubnet**.
4. На странице *mySubnet* в списке **Делегирование подсети** выберите **нет** из списка служб, перечисленных в разделе **Делегирование подсети к службе**. 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для работы с этой статьей необходимо использовать Azure CLI версии 2.0.28 или более поздней. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем **myResourceGroup** в расположении **eastus**.

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
С помощью команды **az network vnet create** создайте виртуальную сеть с именем **myVnet**, содержащую подсеть **mySubnet**, в группе ресурсов [myResourceGroup](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Разрешения

Если вы не создали подсеть, которую вы хотите делегировать службе Azure, вам потребуется следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`.

Встроенная роль « [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) » также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегирование подсети в службу Azure

В этом разделе вы делегируйте подсеть, созданную в предыдущем разделе, в службу Azure. 

Чтобы обновить подсеть с именем **mySubnet** и делегированием к службе Azure, используйте команду [AZ Network vnet подсети Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) .  В этом примере в качестве примера делегирования используется **Microsoft. дбфорпостгрескл/serversv2** .

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet
  --vnet-name myVnet
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Чтобы убедиться, что делегирование было применено, используйте команду [AZ Network vnet подсеть показывать](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Убедитесь, что служба делегирована в подсеть в свойстве **ServiceName**:

```azurecli-interactive
  az network vnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегирования подсети из службы Azure

Чтобы удалить делегирование из подсети с именем **mySubnet**, используйте команду [AZ Network vnet подсети Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) .

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup
  --name mySubnet
  --vnet-name myVnet
  --remove delegations
```
Чтобы проверить, было ли делегирование удалено, используйте команду [AZ Network vnet подсеть показывать](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Убедитесь, что служба удалена из подсети в свойстве **ServiceName**:

```azurecli-interactive
  az network vnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet
  --query delegations
```
Выходные данные команды являются пустой скобкой:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Подключение к Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с именем **myVnet** с подсетью с именем **MySubnet** , используя [New-Азвиртуалнетворксубнетконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) в **myResourceGroup** с помощью [New-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Пространство IP-адресов для виртуальной сети — **10.0.0.0/16**. Подсеть в виртуальной сети — **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Разрешения

Если вы не создали подсеть, которую вы хотите делегировать службе Azure, вам потребуется следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`.

Встроенная роль « [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) » также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегирование подсети в службу Azure

В этом разделе вы делегируйте подсеть, созданную в предыдущем разделе, в службу Azure. 

Используйте [Add-азделегатион](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) для обновления подсети с именем **mySubnet** и делегирования с именем **миделегатион** в службу Azure.  В этом примере в качестве примера делегирования используется **Microsoft. дбфорпостгрескл/serversv2** .

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Чтобы проверить делегирование, используйте команду [Get-азделегатион](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) .

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегирования подсети из службы Azure

Удалите делегирование из подсети с именем **mySubnet**с помощью [Remove-азделегатион](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) :

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Чтобы убедиться, что делегирование было удалено, используйте команду [Get-азделегатион](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) :

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [управлять подсетями в Azure](virtual-network-manage-subnet.md).
