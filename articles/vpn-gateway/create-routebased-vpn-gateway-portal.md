---
title: Создание VPN-шлюза на основе маршрутов. портал Azure | Документация Майкрософт
description: Создание VPN-шлюза на основе маршрутов с помощью портала Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781171"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Создание VPN-шлюза на основе маршрутов с помощью портала Azure

Эта статья поможет быстро создать VPN-шлюз Azure на основе маршрутов, используя портал Azure.  VPN-шлюз используется при создании VPN-подключения к локальной сети. Также вы можете использовать VPN-шлюз для подключения виртуальных сетей. 

Инструкции в этой статье позволяют создать виртуальную сеть, подсеть, подсеть шлюза и VPN-шлюз на основе маршрутов (шлюз виртуальной сети). Создав шлюз, можно создавать подключения. Для этих действий требуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="vnet"></a>Создание виртуальной сети

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>Добавление подсети шлюза

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>Настройка и создание шлюза

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Базовый номер SKU шлюза не поддерживает проверку подлинности IKEv2 или RADIUS. Если вы планируете подключение клиентов Mac к виртуальной сети, не используйте SKU "базовый".

## <a name="viewgw"></a>Просмотр VPN-шлюза

1. После создания шлюза перейдите к сети VNet1 с помощью портала. На странице "Обзор" VPN-шлюз отобразится как подключенное устройство.

   ![Подключенные устройства](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Connected devices")

2. В списке устройств щелкните **VNet1GW**, чтобы получить дополнительные сведения.

   ![Просмотр VPN-шлюза](./media/create-routebased-vpn-gateway-portal/view-gateway.png "View VPN gateway")

## <a name="next-steps"></a>Следующие шаги

Создав шлюз, можно создать подключение между вашей и другой виртуальной сетью. Или же можно создать подключение между виртуальной сетью и локальным расположением.

> [!div class="nextstepaction"]
> [Создание подключения "сеть — сеть"](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Создание подключения "точка — сеть"](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Создание подключения к другой виртуальной сети](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
