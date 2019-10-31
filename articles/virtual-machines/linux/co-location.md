---
title: Совместное размещение виртуальных машин Azure в Linux
description: Узнайте, как совместное размещение ресурсов виртуальных машин Azure может повысить задержку.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: c90e70900c019340cacc169325155ac74cf7807c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171466"
---
# <a name="co-locate-resources-for-improved-latency"></a>Совместное размещение ресурсов для повышения задержки

При развертывании приложения в Azure распределение экземпляров по регионам или зонам доступности создает задержку в сети, что может повлиять на общую производительность приложения. 

## <a name="proximity-placement-groups"></a>Группы размещения с учетом расположения

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Разверните виртуальную машину в [группе размещения](proximity-placement-groups.md) с помощью Azure CLI.

Узнайте, как [проверить задержку в сети](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Узнайте, как [оптимизировать пропускную способность сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Узнайте, как [использовать группы размещения близкого взаимодействия с приложениями SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
