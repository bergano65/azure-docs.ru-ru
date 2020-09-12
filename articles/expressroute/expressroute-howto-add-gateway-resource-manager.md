---
title: 'Azure ExpressRoute: Добавление шлюза в виртуальную сеть: PowerShell'
description: В этой статье объясняется, как добавлять шлюз виртуальной сети в созданную виртуальную сеть Resource Manager для ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfdab553ba7f6506f66e892da3f1e8ce01c6d8bb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396392"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Классическая модель: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Видео — портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

В этой статье объясняется, как добавить, изменить размер и удалить шлюз виртуальной сети для существующей виртуальной сети. Действия для этой конфигурации применяются к виртуальных сетей, которые были созданы с помощью модели развертывания диспетчер ресурсов для конфигурации ExpressRoute. Дополнительные сведения см. в статье [Сведения о шлюзах виртуальных сетей ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Подготовка

### <a name="working-with-powershell"></a>Работа с PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Список ссылок конфигурации

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. Ознакомьтесь со статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).
