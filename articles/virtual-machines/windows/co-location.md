---
title: Совместное налаживать Вымотки Windows Azure
description: Узнайте о том, как совместное размещение ресурсов Azure VM может улучшить задержку.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266797"
---
# <a name="co-locate-resource-for-improved-latency"></a>Совместное использование ресурсов для улучшения задержки

При развертывании приложения в Azure распространение экземпляров в разных регионах или зонах доступности создает задержку сети, что может повлиять на общую производительность приложения. 


## <a name="proximity-placement-groups"></a>Группы размещения близкого взаимодействия 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Развертывание VM в [группу размещения непосредственного устройства](proximity-placement-groups.md) с помощью Azure PowerShell.

Узнайте, как [протестировать задержку сети.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Узнайте, как [оптимизировать пропускную выливку сети.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Узнайте, как [использовать группы размещения непосредственной близости с приложениями SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)