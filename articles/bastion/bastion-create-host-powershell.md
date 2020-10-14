---
title: Создание узла бастиона с помощью Azure PowerShell | Документация Майкрософт
description: В этой статье вы узнаете, как создать узел бастиона для Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: f0e7a66ef7d6947306f1b2ffec54b8e9d12737d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018599"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Создание узла бастиона Azure с помощью Azure PowerShell

В этой статье показано, как создать узел бастиона для Azure с помощью PowerShell. После развертывания бастиона можно подключиться к виртуальной машине через свой частный IP-адрес в браузере с помощью портал Azure. Виртуальной машине не требуется общедоступный IP-адрес, дополнительный клиент или специальное программное обеспечение. Бастион Azure развертывается на уровне виртуальной сети, а не подписки, учетной записи или виртуальной машины. Простой интерфейс RDP/SSH доступен для всех виртуальных машин в одной виртуальной сети.

Эти инструкции предназначены для развертывания с PowerShell. Вы также можете создать узел бастиона для Azure с помощью [портал Azure](tutorial-create-host-portal.md) или [Azure CLI](create-host-cli.md).

## <a name="before-you-begin"></a>Перед началом

Убедитесь в том, что у вас уже есть подписка Azure. Если у вас еще нет подписки Azure, вы можете активировать преимущества для [подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Создание узла-бастиона

Этот раздел поможет вам создать новый ресурс Azure бастиона для виртуальной сети с помощью Azure PowerShell.

1. Создайте виртуальную сеть и подсеть бастиона для Azure. Необходимо создать подсеть бастиона для Azure, используя значение Name **азуребастионсубнет**. По этому значению Azure сможет определить, в какую подсеть следует развертывать ресурсы типа "Бастион". Она отличается от подсети шлюза. Необходимо использовать подсеть как минимум/27 или более крупную подсеть (/27,/26 и т. д.). Создайте **азуребастионсубнет** без таблиц или делегирований маршрутов. Если вы используете группы безопасности сети в **азуребастионсубнет**, см. статью [Работа с группы безопасности сети](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Создайте общедоступный IP-адрес для Azure бастиона. Общедоступные IP-адреса — это общедоступный IP-адрес ресурса бастиона, к которому будут обращаться протоколы RDP/SSH (через порт 443). Общедоступный IP-адрес должен находиться в том же регионе, что и создаваемый ресурс типа "Бастион".

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Создайте новый ресурс Azure бастиона в Азуребастионсубнет виртуальной сети. Создание и развертывание ресурса бастиона занимает около 5 минут.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Подключитесь к виртуальной машине.
   * [Виртуальная машина Linux](bastion-connect-vm-ssh.md)
   * [Виртуальная машина Windows](bastion-connect-vm-rdp.md)
