---
title: Добавление IPv6 в приложение IPv4 в виртуальной сети Azure - Azure CLI
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать адреса IPv6 в существующее приложение в виртуальной сети Azure с помощью Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240754"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Добавьте IPv6 в приложение IPv4 в виртуальной сети Azure - Azure CLI (Предварительный просмотр)

В этой статье показано, как добавить адреса IPv6 в приложение, использующее общедоступный IP-адрес IPv4 в виртуальной сети Azure для балансора стандартной нагрузки с помощью Azure CLI. Обновление на месте включает в себя виртуальную сеть и подсеть, балансер стандартной нагрузки с конфигурациями ipV4 и IPV6, виртуальные компьютеры с NICs, которые имеют конфигурации IPv4 и IPv6, группу сетевой безопасности и публичные IP-адреса.

> [!Important]
> Поддержка IPv6 для виртуальной сети Azure в настоящее время находится в открытом доступе. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Предварительные требования

### <a name="register-the-service"></a>Регистрация услуги

Перед развертыванием приложения с двойным стеком в Azure необходимо настроить подписку на эту функцию предварительного просмотра с помощью следующего clI Azure:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Регистрация функции занимает до 30 минут. Проверить свой регистрационный статус можно, запустив следующую команду Azure CLI:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

После регистрации выполните следующую команду:

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Стандартный"
В этой статье предполагается, что вы развернули балансовик стандартной нагрузки, как описано в [квикстарте: Создайте баланс- стандартный баланс - Azure CLI.](../load-balancer/quickstart-load-balancer-standard-public-cli.md)

## <a name="create-ipv6-addresses"></a>Создание адресов IPv6

Создайте общедоступный адрес IPv6 с помощью [веб-ip-приложения сети az](/cli/azure/network/public-ip) для вашего балансора стандартной нагрузки. Следующий пример создает ip-адрес IPv6 *с* PublicIP_v6 в группе ресурсов *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Настройка баланса нагрузки IPv6 передняя

COnfigure балансиватель нагрузки с новым IP-адресом IPv6 с помощью [az сети lb frontend-IP создать](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) следующим образом:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Настройка пула бэкэнда нагрузоза IPv6

Создайте пул бэкэнда для NICs с адресами IPv6 с помощью [адреса-пула az network lb:](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Настройка правил балансировора нагрузки IPv6

Создайте правила балансобаланса нагрузки IPv6 с [помощью правила az network lb.](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Добавление диапазонов адресов IPv6

Добавьте диапазоны адресов IPv6 в виртуальную сеть и подсеть, где размещается баланселер нагрузки следующим образом:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Добавление конфигурации IPv6 в NICs

Настроите VM NICs с адресом IPv6 с помощью [az network nic ip-config создать](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) следующее:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр виртуальной сети IPv6 с двойным стеком на портале Azure
Виртуальную сеть IPv6 с двойным стеком можно просмотреть следующим образом:
1. В панели поиска портала введите *myVnet*.
2. Когда **myVnet** появится в результатах поиска, выберите его. Это запускает **Обзор** страницы двойного стека виртуальной сети под названием *myVNet*. Виртуальная сеть двойного стека показывает три NICs с конфигурациями IPv4 и IPv6, расположенными в подсети с двойным стеком под названием *mySubnet.*

  ![Виртуальная сеть IPv6 с двойным стеком в Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Виртуальная сеть IPv6 для Azure доступна на портале Azure только для этого предварительного просмотра.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обновили существующий балансироворовую стандартную нагрузку с конфигурацией IP-адреса IPv4 до двойной стек (IPv4 и IPv6). Вы также добавили конфигурации IPv6 в NICs VMs в пул бэкэнда. Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?](ipv6-overview.md)
