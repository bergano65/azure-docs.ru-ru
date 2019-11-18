---
title: Создание узла бастиона с помощью Azure PowerShell | Документация Майкрософт
description: В этой статье вы узнаете, как создать узел бастиона для Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6cae6d258da2ddf0c3bfaade65ae74f1201b67b7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121078"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Создание узла бастиона Azure с помощью Azure PowerShell

В этой статье показано, как создать узел бастиона для Azure. После подготовки службы Azure бастиона в виртуальной сети работа по протоколу RDP и SSH будет доступна для всех виртуальных машин в той же виртуальной сети. Служба развертывается на уровне виртуальной сети, а не подписки, учетной записи или виртуальной машины.

## <a name="before-you-begin"></a>Перед началом работы

Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>Создание узла бастиона

Этот раздел поможет вам создать новый ресурс Azure бастиона с помощью Azure PowerShell.

1. Создайте виртуальную сеть и подсеть бастиона для Azure. Необходимо создать подсеть бастиона для Azure, используя значение Name **азуребастионсубнет**. Это значение позволяет Azure выяснить, в какую подсеть развертывать ресурсы бастиона. Это отличается от подсети шлюза. Необходимо использовать подсеть по крайней мере a/27 или более крупную подсеть (/27,/26 и т. д.). Создайте **азуребастионсубнет** без таблиц или делегирований маршрутов. При использовании групп безопасности сети в **азуребастионсубнет**см. статью [Работа с группы безопасности сети](bastion-nsg.md).

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Создайте общедоступный IP-адрес для Azure бастиона. Общедоступные IP-адреса — это общедоступный IP-адрес ресурса бастиона, к которому будут обращаться протоколы RDP/SSH (через порт 443). Общедоступный IP-адрес должен находиться в том же регионе, что и создаваемый ресурс бастиона.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Создайте новый ресурс Azure бастиона в Азуребастионсубнет виртуальной сети. Создание и развертывание ресурса бастиона занимает около 5 минут.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с [часто задаваемыми вопросами о бастиона](bastion-faq.md).
