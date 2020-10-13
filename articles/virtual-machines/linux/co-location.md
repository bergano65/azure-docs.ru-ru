---
title: Совместное размещение виртуальных машин Linux
description: Узнайте, как совместное размещение ресурсов виртуальных машин Azure для Linux может повысить задержку.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 822d67211982526bdd7a36cb5922f33b448eb7c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973382"
---
# <a name="co-locate-resources-for-improved-latency"></a>Совместное размещение ресурсов для уменьшения задержки

При развертывании приложения в Azure распределение экземпляров по регионам или зонам доступности создает задержку в сети, что может повлиять на общую производительность приложения. 

## <a name="proximity-placement-groups"></a>Группы размещения близкого взаимодействия

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Дальнейшие действия

Разверните виртуальную машину в [группе размещения](proximity-placement-groups.md) с помощью Azure CLI.

Узнайте, как [проверить задержку в сети](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json).

Узнайте, как [оптимизировать пропускную способность сети](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Узнайте, как [использовать группы размещения близкого взаимодействия с приложениями SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).