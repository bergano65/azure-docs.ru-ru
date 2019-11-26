---
title: 'Virtual WAN: Create virtual hub route table to NVA: Azure portal'
description: Virtual WAN virtual hub route table to steer traffic to a network virtual appliance using the portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 3aa5660e5b777364ef9d684debe7e06f42acee6e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482025"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Create a Virtual WAN hub route table for NVAs: Azure portal

This article shows you how to steer traffic from a branch (on-premises site) connected to the Virtual WAN hub to a Spoke Vnet via a Network Virtual Appliance (NVA).

![Схема Виртуальной глобальной сети](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Перед началом работы

Убедитесь, что вы выполнили следующие критерии:

*  You have a Network Virtual Appliance (NVA). A Network Virtual Appliance is a third-party software of your choice that is typically provisioned from Azure Marketplace in a virtual network.

    * A private IP address must be assigned to the NVA network interface.

    * The NVA is not deployed in the virtual hub. Он должен быть развернут в отдельной виртуальной сети (VNet).

    *  The NVA VNet may have one or many virtual networks connected to it. In this article, we refer to the NVA VNet as an 'indirect spoke VNet'. These VNets can be connected to the NVA VNet by using VNet peering. The Vnet Peering links are depicted by black arrows in the above figure.
*  You have created 2 VNets. They will be used as spoke VNets.

    * For this exercise, the VNet spoke address spaces are: VNet1: 10.0.2.0/24 and VNet2: 10.0.3.0/24. If you need information on how to create a VNet, see [Create a virtual network](../virtual-network/quick-create-portal.md).

    * Ensure there are no virtual network gateways in any of the VNets.
    * For this configuration, these VNets do not require a gateway subnet.

## <a name="signin"></a>1. Sign in

В браузере откройте [портал Azure](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.

## <a name="vwan"></a>2. Create a virtual WAN

Создание виртуальной глобальной сети. For the purposes of this exercise, you can use the following values:

* **Virtual WAN name:** myVirtualWAN
* **Resource group:** testRG
* **Location:** West US

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Create a hub

Create the hub. For the purposes of this exercise, you can use the following values:

* **Location:** West US
* **Name:** westushub
* **Hub private address space:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Create and apply a hub route table

Update the hub with a hub route table. For the purposes of this exercise, you can use the following values:

* **Indirect spoke VNet address spaces:** (VNet1 and VNet2) 10.0.2.0/24 and 10.0.3.0/24
* **DMZ NVA network interface private IP address:** 10.0.4.5

1. Navigate to your virtual WAN.
2. Click the hub for which you want to create a route table.
3. Click the **...** , and then click **Edit virtual hub**.
4. On the **Edit virtual hub** page, scroll down and select the checkbox **Use table for routing**.
5. In the **If destination prefix is** column, add the address spaces. In the **Send to next hop** column, add the DMZ NVA network interface private IP address.
6. Click **Confirm** to update the hub resource with the route table settings.

## <a name="connections"></a>5. Create the VNet connections

Create a connection from each indirect spoke VNet (VNet1 and VNet2) to the hub. Then, create a connection from the NVA VNet to the hub. These Vnet Connections are dipicted by blue arrows in the figure above. 

 For this step, you can use the following values:

| Имя виртуальной сети| Имя подключения|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repeat the following procedure for each VNet that you want to connect.

1. На странице своей глобальной сети щелкните **Подключения к виртуальной сети**.
2. На странице подключения к виртуальной сети щелкните **+Добавить подключение**.
3. На странице **Добавление подключения** заполните следующие поля.

    * **Имя подключения**. Укажите имя своего подключения.
    * **Концентраторы**. Выберите концентратор, который нужно связать с этим подключением.
    * **Подписка**. Проверьте подписку.
    * **Виртуальная сеть**. Выберите виртуальную сеть, которую вы хотите подключить к этому концентратору. В виртуальной сети не должно быть шлюза виртуальной сети.
4. Нажмите кнопку **ОК**, чтобы создать подключение.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).
