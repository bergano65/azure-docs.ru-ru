---
title: Добавление или удаление делегации подсети в виртуальной сети Azure
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
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201872"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Добавление или удаление делегации подсети

Делегирование подсети прямо разрешает службе в процессе развертывания создавать в подсети необходимые для этой службы ресурсы с помощью уникального идентификатора. В этой статье описывается, как добавить или удалить делегированную подсеть для службы Azure.

## <a name="portal"></a>Портал

### <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

В этом разделе создается виртуальная сеть и подсеть, которую позже делегируете службе Azure.

1. На верхней левой стороне экрана выберите **Создать ресурс** > **Сети** > **Виртуальная сеть.**
1. В подменю **Создать виртуальную сеть** введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | name | Введите *MyVirtualNetwork*. |
    | Пространство адресов | Введите *10.0.0.0/16*. |
    | Подписка | Выберите свою подписку.|
    | Группа ресурсов | Выберите **Создать**, а затем введите *myResourceGroup* и нажмите кнопку **ОК**. |
    | Расположение | Выберите **EastUS**.|
    | Имя подсети | Введите *mySubnet*. |
    | Диапазон адреса подсети | Введите *10.0.0.0/24*. |
    |||
1. Оставьте остальное по умолчанию, а затем выберите **Создать**.

### <a name="permissions"></a>Разрешения

Если вы не создали подсеть, которая вы хотите делегировать службе Azure, вам нужно следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`

Встроенная роль [сетевого вкладчика](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегировать подсеть в службу Azure

В этом разделе вы делегирует подсеть, созданную в предыдущем разделе, службе Azure.

1. На портале в панели поиска введите *myVirtualNetwork*. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его.
2. В результатах поиска выберите *myVirtualNetwork*.
3. Выберите **подсети,** под **SETTINGS**, а затем выберите **mySubnet**.
4. На странице *mySubnet* для списка **делегаций Subnet** выберите из служб, перечисленных в **подсети Delegate, в службу (например,** **Microsoft.DBforPostgreS'L/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегации подсети из службы Azure

1. На портале в панели поиска введите *myVirtualNetwork*. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его.
2. В результатах поиска выберите *myVirtualNetwork*.
3. Выберите **подсети,** под **SETTINGS**, а затем выберите **mySubnet**.
4. На странице *mySubnet* для списка **делегаций Subnet** выберите **ни одного** из служб, перечисленных в **подсети Delegate, в службу.** 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вместо этого вы решили установить и использовать Azure CLI локально, эта статья требует, чтобы вы использовали версию Azure CLI 2.0.28 или позже. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Следующий пример создает группу ресурсов под названием **myResourceGroup** в **восточном** месте:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть под названием **myVnet** с подсетью под названием **mySubnet** в **myResourceGroup** с помощью [az сети vnet создать](https://docs.microsoft.com/cli/azure/network/vnet).

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

Если вы не создали подсеть, которая вы хотите делегировать службе Azure, вам нужно следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`

Встроенная роль [сетевого вкладчика](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегировать подсеть в службу Azure

В этом разделе вы делегирует подсеть, созданную в предыдущем разделе, службе Azure. 

Используйте [обновление подсети сети az для](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) обновления подсети **подназвание mySubnet** с делеготом в службу Azure.  В этом примере для примерной делегации используется примерная делегация **Microsoft.DBforPostgreS'L/serversv2:**

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Для проверки делегирования было применено, используйте [az сети Vnet подсеть шоу](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Проверка службы делегируется в подсеть под **сервисом свойствName:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
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

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегации подсети из службы Azure

Используйте [обновление подсети сети az для](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) удаления делегации из подсети под названием **mySubnet:**

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Чтобы проверить, что делегация была удалена, используйте [поднет-сеть az network vnet.](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Проверка службы удаляется из подсети под **сервисом свойствName:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Выход из команды является нулевой кронштейн:
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

Следующий пример создает группу ресурсов под названием *myResourceGroup* в *восточном* месте:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть под названием **myVnet** с подсетью под названием **mySubnet** с помощью [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) в **myResourceGroup** с помощью [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Пространство IP-адреса для виртуальной сети **10.0.0.0.0/16**. Подсеть в виртуальной сети **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Разрешения

Если вы не создали подсеть, которая вы хотите делегировать службе Azure, вам нужно следующее разрешение: `Microsoft.Network/virtualNetworks/subnets/write`

Встроенная роль [сетевого вкладчика](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) также содержит необходимые разрешения.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Делегировать подсеть в службу Azure

В этом разделе вы делегирует подсеть, созданную в предыдущем разделе, службе Azure. 

Используйте [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) для обновления подсети под названием **mySubnet** с делегацией под названием **myDelegation** в службу Azure.  В этом примере для примерной делегации используется примерная делегация **Microsoft.DBforPostgreS'L/serversv2:**

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Используйте [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) для проверки делегирования:

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
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Удаление делегации подсети из службы Azure

Используйте [Удалить-AzDelegation,](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) чтобы удалить делегацию из подсети под названием **mySubnet:**

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Используйте [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) для проверки удаления делегации:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [управлять подсетями в Azure.](virtual-network-manage-subnet.md)
