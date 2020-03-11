---
title: Совместное размещение виртуальных машин Linux
description: Узнайте, как совместное размещение ресурсов виртуальных машин Azure может повысить задержку.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967879"
---
# <a name="co-locate-resources-for-improved-latency"></a>Совместное размещение ресурсов для повышения задержки

При развертывании приложения в Azure распределение экземпляров по регионам или зонам доступности создает задержку в сети, что может повлиять на общую производительность приложения. 

## <a name="proximity-placement-groups"></a>Группы размещения близкого взаимодействия

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Разверните виртуальную машину в [группе размещения](proximity-placement-groups.md) с помощью Azure CLI.

Узнайте, как [проверить задержку в сети](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Узнайте, как [оптимизировать пропускную способность сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Узнайте, как [использовать группы размещения близкого взаимодействия с приложениями SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
