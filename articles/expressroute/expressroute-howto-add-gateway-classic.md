---
title: 'Azure ExpressRoute: Добавление шлюза в виртуальную сеть: классическая модель'
description: Узнайте, как настроить шлюз виртуальной сети для виртуальной сети на основе классической модели развертывания с помощью командлетов PowerShell для настройки ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bcffd60596f8c2286b91eaf13be3e09bb8bec94f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736413"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell (классическая модель)
> [!div class="op_single_selector"]
> * [Resource Manager — PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Классическая модель: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Видео — портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Эта статья содержит инструкции по добавлению, изменению размера и удалению шлюза виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью **классической модели развертывания**, которые будут использоваться в конфигурации ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Подготовка
Убедитесь, что установлены командлеты Azure PowerShell, необходимые для этой конфигурации.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Дальнейшие шаги
После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. Ознакомьтесь со статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md).

