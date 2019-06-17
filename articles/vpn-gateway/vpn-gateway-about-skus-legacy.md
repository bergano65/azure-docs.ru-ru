---
title: Устаревшие SKU VPN-шлюзов виртуальной сети Azure | Документация Майкрософт
description: 'Как работать со старыми номерами SKU шлюзов виртуальной сети: Basic, Standard и HighPerformance.'
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: cherylmc
ms.openlocfilehash: 00f1677e2691f9be5bb4584b07ca00340a52b1e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056435"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Работа со SKU шлюза виртуальной сети (старые версии SKU)

Эта статья содержит сведения о старых версиях SKU шлюза виртуальной сети. Старые версий SKU по-прежнему работают в обеих моделях развертывания для созданных VPN-шлюзов. Классические VPN-шлюзы по-прежнему используют старые номера SKU как для имеющихся шлюзов, так и для новых. При создании VPN-шлюзов диспетчера ресурсов используйте новые номера SKU шлюза. Сведения о новых номерах SKU см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU шлюзов

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Вы можете просмотреть цены устаревший шлюз в **шлюзы виртуальной сети** раздела, в котором расположен в на [странице цен на ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="agg"></a>Расчетная суммарная пропускная способность в зависимости от SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Поддерживаемые конфигурации в зависимости от SKU и типа VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Изменение размера шлюза

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете изменить размер шлюза, сменив номер SKU шлюза на другой в пределах одного семейства SKU. Например, при наличии номера SKU " Стандартный" можно изменить размер до SKU HighPerformance. Но размер VPN-шлюза невозможно изменить путем перехода со старого номера SKU на номер из нового семейства SKU. Например, вы не сможете перейти с номера SKU типа Standard на VpnGw2 или с номера SKU типа Basic на VpnGw1.

Чтобы изменить размер шлюза, созданный на базе классической модели развертывания, используйте следующую команду:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Чтобы изменить размер шлюза, созданный на базе модели развертывания с помощью PowerShell, используйте следующую команду:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Можно также изменить размер шлюза на портале Azure.

## <a name="change"></a>Переход на новые номера SKU шлюзов

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о новых версиях SKU шлюзов см. в разделе [SKU шлюзов](vpn-gateway-about-vpngateways.md#gwsku).

Дополнительные сведения о параметрах конфигурации VPN-шлюза см. в [этой статье](vpn-gateway-about-vpn-gateway-settings.md).
