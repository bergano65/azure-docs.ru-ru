---
title: Совместное место ВМ для улучшения задержки
description: Узнайте о том, как совместное размещение ресурсов Azure VM может улучшить задержку.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083178"
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