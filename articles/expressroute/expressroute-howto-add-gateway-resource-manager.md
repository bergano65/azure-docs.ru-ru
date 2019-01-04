---
title: Добавление шлюза виртуальной сети для ExpressRoute с помощью PowerShell в Azure | Документация Майкрософт
description: В этой статье объясняется, как добавлять шлюз виртуальной сети в созданную виртуальную сеть Resource Manager для ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097919"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Классическая модель: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Видео — портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

В этой статье объясняется, как добавить, изменить размер и удалить шлюз виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью модели развертывания Resource Manager, которые будут использоваться в конфигурации ExpressRoute. Дополнительные сведения о шлюзах виртуальных сетей и параметрах конфигурации шлюза для ExpressRoute см. в разделе [Сведения о шлюзах виртуальных сетей ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Подготовка
Убедитесь, что у вас установлена последняя версия командлетов Azure PowerShell. Если вы еще не установили ее, необходимо сделать это перед началом настройки. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Дополнительная информация
После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. Ознакомьтесь со статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

