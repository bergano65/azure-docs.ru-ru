---
title: Совместное размещения Linux VMs
description: Узнайте о том, как совместное размещение ресурсов Azure VM может улучшить задержку.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250287"
---
# <a name="co-locate-resources-for-improved-latency"></a>Совместное распорядивание ресурсов для улучшения задержки

При развертывании приложения в Azure распространение экземпляров в разных регионах или зонах доступности создает задержку сети, что может повлиять на общую производительность приложения. 

## <a name="proximity-placement-groups"></a>Группы размещения близкого взаимодействия

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Развертывание VM в [группу размещения непосредственной близости](proximity-placement-groups.md) с помощью Azure CLI.

Узнайте, как [протестировать задержку сети.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Узнайте, как [оптимизировать пропускную выливку сети.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Узнайте, как [использовать группы размещения непосредственной близости с приложениями SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
