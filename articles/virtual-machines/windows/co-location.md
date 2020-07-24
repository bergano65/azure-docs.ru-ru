---
title: Совместное размещение виртуальных машин для повышения задержки
description: Узнайте, как совместное размещение ресурсов виртуальных машин Azure может повысить задержку.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 85832fe56b064916b50a63bf4b399f0a21afaaac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028163"
---
# <a name="co-locate-resource-for-improved-latency"></a>Совместное размещение ресурса для повышения задержки

При развертывании приложения в Azure распределение экземпляров по регионам или зонам доступности создает задержку в сети, что может повлиять на общую производительность приложения. 


## <a name="proximity-placement-groups"></a>Группы размещения близкого взаимодействия 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Разверните виртуальную машину в [группе размещения](proximity-placement-groups.md) с помощью Azure PowerShell.

Узнайте, как [проверить задержку в сети](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Узнайте, как [оптимизировать пропускную способность сети](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Узнайте, как [использовать группы размещения близкого взаимодействия с приложениями SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
