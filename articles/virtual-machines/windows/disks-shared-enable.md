---
title: Включить общие диски для управляемых дисков Azure
description: Налажить управляемый azure диск с общими дисками (предварительным просмотром), чтобы вы могли делиться им через несколько VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1cb581f0914dbafff4a46510924115e82e42b990
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011146"
---
# <a name="enable-shared-disk"></a>Включить общий диск

В этой статье рассказывается о том, как включить функцию общих дисков (предварительный просмотр) для управляемых дисков Azure. Общие диски Azure (предварительный просмотр) — это новая функция для управляемых дисков Azure, которая позволяет одновременно прикреплять управляемый диск к нескольким виртуальным машинам (Виртуальным машинам). Присоединение управляемого диска к нескольким ВМ позволяет развертывать новые или мигрировать в Azure. 

Если вы ищете концептуальную информацию о управляемых дисках, которые имеют общий обмен дисками, обратитесь к [общим дискам Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]